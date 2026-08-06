---
doc_type: fluid_build_system_guide
status: active
created: 2026-01-25
updated: 2026-08-06
---

# FBS — Fluid Build System — Setup Guide (Portable)

This guide explains the **FBS (Fluid Build System)** and how to reproduce it in a different project so an AI agent (in Claude Code) can build confidently with clear governance and low drift.

## Upstream compatibility note

During installation, check the current Claude Code docs for any changes since this kit was created:

- [CLAUDE.md and Memory](https://code.claude.com/docs/en/memory)
- [Skills](https://code.claude.com/docs/en/skills)
- [Subagents](https://code.claude.com/docs/en/sub-agents)
- [Settings](https://code.claude.com/docs/en/settings)

---

## 1) What FBS is

FBS is a **documentation + governance architecture** for AI-assisted development:

- **AI should not "infer" contracts** (schemas, API shapes, business rules). Those are **written canonically**.
- **AI should not load everything**. It should load a small set of **high-signal documents** and the **relevant skills**.
- **Work should be tracked** (current status + sprint doc), and drift should be caught early via **audits + fast checks**.

FBS is built from four pillars:

1. **Rules** (`CLAUDE.md` + `.claude/rules/`): always-on guardrails plus two path-scoped workflow rules.
2. **Skills** (`.claude/skills/`): progressive-disclosure guidance, loaded on demand.
3. **Documents** (`docs/ideas/`, `docs/plans/`, `docs/sprints/`): explore → decide → execute, with strictly separated jobs.
4. **Audits** (`docs/audits/`): drift detection + remediation tracking.

FBS ships **no hooks, no agent definitions, and no `settings.json`**. It does not intercept tool calls or impose a permissions model. Everything it does is documentation and skills, which means it can be adopted incrementally and removed cleanly.

---

## 2) System structure

### 2.1 The "resume fast" path

**In order**:

- `CLAUDE.md`: project instructions, tier routing, and the one governance gate (always loaded).
- `PROJECT_STATUS.md`: single-page resume context snapshot.
- `docs/sprints/CURRENT_STATUS.md`: what's happening now (active sprint, blockers, stage).
- `docs/audits/active/`: unresolved drift findings.
- `.claude/skills/`: load only relevant skills.

### 2.2 The execution loop (how features get built)

FBS has **three tiers**. The first decision on any task is which tier it is, and the agent must say which one it picked.

| Tier | Trigger | Path |
|------|---------|------|
| **1 — Small fix** | 1-3 files, no contract change | Implement directly. No documents. |
| **2 — Sprint** | Multi-file feature or refactor, no contract change | `/new-sprint` → `/start-sprint` → `/review-sprint` |
| **3 — Plan + sprint** | Schema, API shape, integration payload, or documented business rule changes | `/new-idea` → `/new-plan` → ⏸ approval → `/new-sprint` → `/start-sprint` → `/review-sprint` |

Optional checks, run by judgement rather than by rule:

| Check | Runs after | Purpose |
|-------|-----------|---------|
| `/review-idea-doc` | `/new-idea` | Confirms the idea settled on a single direction |
| `/check-plan` | `/new-plan` | Feasibility against the codebase + plan-discipline checks |
| `/check-sprint` | `/start-sprint` | Deep code review of the diff; worth it above ~15 changed files |
| `/doc-sprint-sync` | `/review-sprint` | Refresh canonical docs |

### 2.3 Governance gates

There is exactly **one blocking gate**: a contract-impacting change requires an approved plan before implementation. Everything else in FBS is a default that can be overridden by saying so.

**Plans** are required before implementing:
- API contract changes (field names/shapes, error envelopes, endpoint semantics)
- Database schema changes (tables/columns/constraints/migrations)
- Integration contract changes (webhooks, external service payloads)
- Canonical business rule changes

**Audits** are used to:
- detect drift between implementation and canonical docs
- verify new work didn't break governance rules
- create structured remediation plans (P0/P1 first)

---

## 3) Documentation rules

### 3.1 Documentation hierarchy

1. **Operational entrypoints**: `CLAUDE.md`, `PROJECT_STATUS.md`, `docs/sprints/CURRENT_STATUS.md`
2. **Canonical documentation**: platform architecture, schemas, API contracts, business rules
3. **Plans and decisions**: `docs/plans/`, `docs/ideas/`
4. **Drift tracking**: `docs/audits/`
5. **Progressive-disclosure guidance**: `.claude/skills/`
6. **Roadmap**: `docs/roadmap/ROADMAP.md`
7. **Solutions library**: `docs/solutions/`

### 3.2 Document naming conventions

Ideas, plans, and sprints use sequential numbering for unambiguous referencing:

| Type | Format | Example |
|------|--------|---------|
| Ideas | `IDEA-NNN-YYYY-MM-DD-<topic>.md` | `IDEA-001-2026-03-13-user-authentication.md` |
| Plans | `PLAN-NNN-YYYY-MM-DD-<topic>.md` | `PLAN-001-2026-03-13-auth-schema-changes.md` |
| Sprints | `SPRINT-NNN-YYYY-MM-DD-<topic>.md` | `SPRINT-001-2026-03-13-auth-implementation.md` |

- All three use globally sequential numbers (zero-padded to 3 digits).
- The `number` field in frontmatter must match the filename number.
- To determine the next number, scan the directory for existing files.

### 3.3 What "canonical" means

Canonical documentation is authoritative. If canonical says "X" and code does "Y", that's drift.

- One canonical source per contract (no duplication).
- Versioned where it matters (minor vs major changes are explicit).
- Traceable: link to implementing code and tests when feasible.

### 3.4 Maintenance

- Edit canonical docs in place; use git history as the change log.
- If a canonical change is required, write a plan first.
- Don't create docs for one-off fixes; update snapshot docs instead.

---

## 4) Install Documentation (one-time)

A one-time process establishing the repo's canonical documentation system (via `/install-documentation`). This is a required step — not optional.

### 4.1 How it works

`/install-documentation` launches sub-agents in parallel to explore the codebase and create comprehensive canonical docs based on what actually exists in the repo. Three sub-agents handle:

- **Platform Overview** — reads README, config files, and directory structure to document what the project is, its architecture, users, and invariants.
- **Schema & Contracts** — searches for database models, API routes, type definitions, and validation schemas to document the entity model and API contracts.
- **Global Terminology** — identifies domain-specific terms, entity names, and business language used in the codebase.

### 4.2 Canonical doc set

`docs/reference/` holds facts about **your system** and nothing else. Four docs, all populated by sub-agents from the actual codebase:

1. `docs/reference/PLATFORM_OVERVIEW_CANONICAL.md`
2. `docs/reference/SCHEMA_AND_CONTRACTS_CANONICAL.md`
3. `docs/reference/GLOBAL_TERMINOLOGY_INDEX_CANONICAL.md`
4. `docs/reference/DOCUMENTATION_INVENTORY.md`

Documentation *standards* — structure, tone, precedence, versioning, supersession, gap handling — live in the `doc-write` skill, loaded on demand. Earlier FBS versions shipped them as four canonical docs totalling ~1,300 lines of documentation-about-documentation into every project before it had written a line of its own. They were removed and folded into `doc-write`.

### 4.3 "No invention" rule

Document what exists. Don't silently introduce new fields, behaviors, or contracts. Gaps follow the plan/approval workflow.

### 4.4 Verification

`/verify-install` checks whether canonical docs are populated or still contain placeholder markers (`YYYY-MM-DD`, bracket placeholders, ellipsis-only sections). Unpopulated docs are flagged with a prompt to re-run `/install-documentation`.

---

## 5) Ongoing Documentation (decoupled from sprint close)

Canonical doc maintenance is a **separate workflow** from sprint execution. Sprints close on verification only; documentation is refreshed manually after the fact when the owner decides it's worth doing.

### 5.1 The four doc skills

| Skill | When | Notes |
|-------|------|-------|
| `/install-documentation` | Once, during FBS setup | Sub-agents explore the codebase and create the initial canonical doc set |
| `/doc-audit` | On demand | Scans the codebase against existing canonical docs; produces a prioritised gap report |
| `/doc-sprint-sync` | After a sprint | Manually invoked. Reads the sprint diff, updates affected canonical docs |
| `/doc-write` | Auto-loaded | Writing standards (schema + example + semantics, tone, prohibited words). Not user-invocable. |

### 5.2 Sprint Definition of Done

Required before sprint stage moves to `done`:
- Implementation matches acceptance criteria
- Required tests/build checks pass
- Contract-impacting changes have approved plans
- Status docs updated

Recommended, surfaced as a reminder at close but **not** gating:
- Mini audit, if canonical surfaces were touched
- Canonical-doc refresh via `/doc-sprint-sync`, if contracts changed

### 5.3 Audit role

Mini and in-depth audits flag documentation drift but do not block sprint close. Address drift via `/doc-audit` or `/doc-sprint-sync` when worth doing.

---

## 6) Claude Code configuration system

### 6.1 CLAUDE.md hierarchy

Claude Code loads instructions from multiple scopes, merged at startup:

| Scope | File | Priority | Shared? |
|-------|------|----------|---------|
| User | `~/.claude/CLAUDE.md` | Lowest | No |
| Project | `CLAUDE.md` or `.claude/CLAUDE.md` | Middle | Yes (committed) |
| Local | `CLAUDE.local.md` | Highest | No (gitignored) |

Subdirectory `CLAUDE.md` files are loaded dynamically when Claude works in that directory. Use these for monorepo package-specific instructions.

### 6.2 Rules

Rules live in `.claude/rules/` as Markdown files, discovered recursively. A rule with **no** `paths:` frontmatter loads unconditionally at launch, with the same priority as `.claude/CLAUDE.md`. A rule **with** `paths:` loads only when Claude reads a file matching one of its globs.

```markdown
---
paths:
  - "docs/sprints/**"
---
```

Because unconditional rules cost context in every session, FBS keeps only two rules, and both are path-scoped:

- `workflow-sprints.md` (`docs/sprints/**`) — stage tracking, concurrent-sprint policy, definition of done
- `workflow-audits.md` (`docs/audits/**`) — drift detection and remediation

Everything that genuinely must always be in context — tier routing, the approval gate, numbering, progressive disclosure — lives in `CLAUDE.md` instead. Earlier versions of FBS shipped `foundation.md` and `workflow-small-fixes.md` as unconditional rules that largely duplicated `CLAUDE.md`; both were removed and folded in.

Rules are context, not enforcement. Claude reads them and generally follows them; nothing guarantees compliance.

### 6.3 Skills

Skills are the primary extension mechanism. Each skill is a directory under `.claude/skills/` containing a `SKILL.md` file.

**Discovery**: Claude loads skill descriptions into context at startup. Full content loads only when a skill is invoked (by user or Claude).

**Frontmatter fields**:

| Field | Purpose |
|-------|---------|
| `name` | Display label in listings. For project skills the `/command` comes from the **directory name**, not this field — keep them identical anyway. |
| `description` | What it does and when to use it (primary trigger mechanism) |
| `argument-hint` | Autocomplete hint, e.g. `[sprint doc path]` |
| `disable-model-invocation` | `true` = only the user can invoke via `/name` |
| `user-invocable` | `false` = hidden from `/` menu, Claude auto-loads when relevant |
| `context` | `fork` = runs in an isolated subagent context with no conversation history |
| `background` | Only with `context: fork`. Defaults to `true`. Set `false` to get the result in the invoking turn. |
| `agent` | Which subagent type when `context: fork` |
| `allowed-tools` | **Pre-approves** tools for the invoking turn. Does **not** restrict anything. |
| `disallowed-tools` | Removes tools from the pool while the skill is active. This is the field that actually restricts. |

Two traps worth knowing, both of which earlier FBS versions fell into:

- `allowed-tools: Read, Glob, Grep` does **not** make a skill read-only. Every other tool remains callable. Use `disallowed-tools: Edit, Write, NotebookEdit` for that.
- `context: fork` backgrounds the skill by default, so its findings arrive asynchronously. A skill that gates a decision — like `/check-plan` before approval — needs `background: false`. A forked skill also can't see the conversation, so it must never end a step with "ask the user"; give it a deterministic default instead.

FBS uses forks only for `/check-plan` and `/review-idea-doc`, both read-only reviewers with `background: false`. The audit skills write files and need conversation context, so they run inline.

**Included skills** (17 total):

Governance pipeline (in order):
- `new-idea` — explore alternatives, land on one direction, create the idea artifact
- `review-idea-doc` — confirm the idea decided something before it becomes a plan
- `new-plan` — the authoritative spec for one contract change, producing one sprint
- `check-plan` — feasibility against the codebase + plan-discipline checks
- `new-sprint` — agent-executable sprint doc with parallel workstreams
- `start-sprint` — execute, delegating concurrent tracks to general-purpose sub-agents
- `check-sprint` — deep code review of the diff, fanning out by review dimension on large diffs
- `review-sprint` — verification gates + close-out

Audits:
- `mini-audit` — targeted drift check
- `in-depth-audit` — comprehensive subsystem audit

Documentation (decoupled from sprint close):
- `install-documentation` — one-time canonical doc setup
- `doc-audit` — gap report between codebase and canonical docs
- `doc-sprint-sync` — manual post-sprint canonical doc refresh
- `doc-write` — writing standards (`user-invocable: false`, loaded by the three above)

Utility:
- `sync` — sync local repo with remote (fetch, pull, prune)
- `compound` — document solved problems
- `verify-install` — verify FBS installation

### 6.4 Sub-agents

FBS ships **no custom agent definitions**. This is deliberate. Bundled reviewer agents drift out of date, duplicate what `/check-sprint` already does, and impose model and tool choices that belong to the project rather than the framework.

Parallelism comes from the documents instead:

- **`/start-sprint`** reads the sprint's **Parallel Workstreams** table and delegates each concurrent track to its own general-purpose sub-agent, launched together in a single message. Each track owns a disjoint set of files, which is what makes concurrency safe.
- **`/check-sprint`** fans out on large diffs by **review dimension** — correctness, security, data integrity, performance, test quality — passing every sub-agent the explicit changed-file list, then deduplicating the findings.

If you want project-specific agents, add them under `.claude/agents/`. Nothing in FBS will overwrite or depend on them. The frontmatter reference is in the [subagents docs](https://code.claude.com/docs/en/sub-agents); note that `tools` is an allowlist, `disallowedTools` is a denylist, and `disallowedTools` is applied first.

### 6.5 Hooks

FBS ships **no hooks**. Earlier versions included a shell guard, a secret-file read guard, and a formatter stub, plus `SessionStart` and `Stop` reminders. They were removed for three reasons:

- The guards depended on `jq` and exited 0 when it was absent, so they failed **open** — silently permitting exactly what they advertised as blocked.
- The `SessionStart` message claimed context had been compacted, which is not what that event means.
- A bare `echo` on a `Stop` hook goes to the transcript, not to Claude, so the reminder never reached the model.

Hooks are a real and useful mechanism, but they belong to the project, not to a portable documentation kit. If you add them, see the [hooks docs](https://code.claude.com/docs/en/hooks), and make security-relevant guards fail **closed**.

### 6.6 Settings

FBS ships **no `.claude/settings.json`**. With hooks removed there was nothing left in it — the old file's `permissions.allow: ["Read", "Glob", "Grep"]` was a no-op, since those tools need no approval by default.

Configure permissions, environment variables, and hooks in your own `settings.json` per the [settings docs](https://code.claude.com/docs/en/settings). Personal overrides go in `.claude/settings.local.json`, which the template `.gitignore` already excludes.

---

## 7) Plans: governance gate for contract changes

### 7.1 When plans are required

- schemas, API contracts, integration contracts, business rules

Not required for:
- purely internal refactors
- bug fixes restoring compliance with canonical spec

### 7.2 What a plan is (and isn't)

A plan is the **authoritative record of a decision already made**. Three rules define it:

**One direction, stated as fact.** Written in the indicative: "the `orders` table gains a `settled_at` timestamp," not "we could add a timestamp." A plan template deliberately has **no Alternatives Considered section** — alternatives are the idea doc's job, and carrying them into a plan is the single biggest cause of ambiguous implementation. `/check-plan` flags hedging language ("maybe", "possibly", "one approach", "alternatively", "TBD", "either/or", "depending on") as P0.

**One plan, one sprint.** A plan produces exactly one sprint. Phased plans implying multiple sprints are three plans, not one. Phases *within* the sprint (DB → API → UI) are execution ordering and are fine. If the implementation impact exceeds one sprint, split into several independently approvable plans and record the dependency in Traceability.

**Specific enough to build from.** Every file path real, every schema matching the project's actual patterns.

The only permitted uncertainty is a Risk with a named mitigation, or an explicit Open Decision — and any Open Decision blocks approval.

### 7.3 Plan workflow

1. Explore via `/new-idea` — this is where alternatives get weighed and one is chosen
2. (Optional) Validate the idea decided something via `/review-idea-doc`
3. Create the plan via `/new-plan` — carries forward the decision only
4. (Optional) Check feasibility and plan discipline via `/check-plan`
5. Wait for explicit user approval
6. Create the single sprint via `/new-sprint`
7. Implement via `/start-sprint`
8. (Optional) Code review via `/check-sprint`
9. Verify and close via `/review-sprint`
10. (Optional) Refresh canonical docs via `/doc-sprint-sync`
11. Mark the plan `status: implemented` and record the sprint number

---

## 8) Sprints: spec-driven execution

### 8.1 Current status

`docs/sprints/CURRENT_STATUS.md` is the single source of truth for what sprint is active, its stage, and blockers. Update it whenever priorities or blockers change.

### 8.2 Sprint docs

Required: stage (`planning | in_progress | verification | done`), acceptance criteria, parallel workstreams, test plan, and a link to the single source plan if contracts change.

### 8.3 Parallel workstreams

This is what makes a sprint fast. The sprint doc declares named **tracks**, each recording:

| Field | Purpose |
|-------|---------|
| Track | Name and the domain(s) it covers |
| Owns | The files or directories it exclusively controls |
| Depends on | Another track, or nothing |
| Done when | How a sub-agent knows it finished |

**No two concurrent tracks may list the same file.** Disjoint ownership is the entire safety mechanism — without it, parallel sub-agents overwrite each other and cost more than they save.

The sprint also states an execution order. The typical shape is a sequential spine with parallel branches: schema lands first, then API and UI tracks run concurrently against it, then integration tests join them.

`/start-sprint` reads this table and launches each concurrent track as its own general-purpose sub-agent, all in one message. Work that genuinely can't be split gets one track — don't invent parallelism.

### 8.4 Stage progression

```
planning -> in_progress -> verification -> done
```

- `/start-sprint` handles planning -> in_progress
- `/check-sprint` optionally performs deep code review after implementation
- `/review-sprint` handles verification -> done (the only way to reach done)

### 8.5 Concurrent sprints

More than one sprint may be open at once. `/start-sprint` names any other open sprints and their stages as a **one-line advisory warning**, then continues. Ideally an earlier sprint has reached `verification` or `done` first, but this never blocks work and never prompts for permission.

---

## 9) Audits: drift detection

### 9.1 Mini audit

After cross-cutting changes, contract touchpoint changes, or suspected drift. Goal: confirm critical points match canonical docs.

### 9.2 In-depth audit

Monthly or at major milestones. Goal: comprehensive verification + remediation plan.

### 9.3 Lifecycle

- Write to `docs/audits/active/`
- At most one active mini and one active in-depth audit at a time
- Move to `docs/audits/resolved/YYYY-MM/` when resolved

---

## 10) Roadmap

`docs/roadmap/ROADMAP.md` is the only document that answers "why are we building this now?" It is optional — delete the directory and every skill skips its roadmap step.

### 10.1 How it stays current

The roadmap is maintained by the sprint lifecycle, not by hand. Hand-maintained roadmaps go stale within a month.

| When | What happens | Who |
|------|--------------|-----|
| Plan created | Plan added to its phase's **Related plans** | `/new-plan` |
| Sprint created | `roadmap_phase:` set in sprint frontmatter; sprint added to that phase's **Related sprints**; phase moves `not started` → `in progress` | `/new-sprint` |
| Sprint closed | Phase deliverables ticked; if all are done, phase → `completed` and the next phase → `in progress` | `/review-sprint` |

`PROJECT_STATUS.md` carries `roadmap_phase:` so a session knows the current phase without opening the roadmap.

### 10.2 Writing phases

Deliverables are checkboxes, and they are the unit `/review-sprint` ticks. Write them as observable outcomes ("users can reset a password by email") rather than activities ("build password reset"). An activity can't be verified as done.

A sprint may advance no phase — bug fixes and maintenance usually don't. Set `roadmap_phase: ""`. Nothing blocks.

---

## 11) Traceability

FBS's document links are bidirectional and machine-checkable, which is what lets `/verify-install` catch broken governance rather than just missing files.

```
IDEA-NNN  ──related_plans──▶  PLAN-NNN  ──Implementation Record──▶  SPRINT-NNN
                                  ▲                                      │
                                  └──────────── plan: frontmatter ───────┘
```

- A sprint's `plan:` frontmatter names its source plan.
- That plan's **Implementation Record → Sprint** names the same sprint.
- `/new-sprint` checks both directions before creating anything, and refuses to create a second sprint for a plan that already has one.
- `/review-sprint` fills the Implementation Record on close. An unfilled record breaks the check for the next person.
- `/verify-install` validates the whole graph: dangling `plan:` pointers, plans whose record names a different sprint, two sprints claiming one plan, and `roadmap_phase:` values that name no real phase.

---

## 12) Knowledge compounding

Use `/compound` after solving a non-obvious problem to create a solution entry under `docs/solutions/` with symptom, root cause, fix, and a prevention step (a test, invariant, or repeatable check). Solutions that turn out to be reusable procedures get promoted to skills.

FBS ships no skill-creation skill. Claude Code has its own `/skill-creator`; vendoring a copy into every project only guarantees it drifts from upstream. Keep new skills under 500 lines and move reference material into supporting files.

---


## 13) Installing into an existing project

### Phase A — Minimal "resume fast"

- Add `CLAUDE.md` to the repo root
- Add `PROJECT_STATUS.md`
- Add `docs/sprints/CURRENT_STATUS.md`

### Phase B — Tier 2 execution

- Add `docs/sprints/` with `sprint-template.md` and `CURRENT_STATUS.md`
- Add `/new-sprint`, `/start-sprint`, `/review-sprint`
- Add `.claude/rules/workflow-sprints.md`
- Start tracking work with sprint docs and stage progression

### Phase C — Tier 3 approval gate

- Add `docs/plans/` and `docs/ideas/` with their templates
- Add `/new-idea`, `/new-plan`
- Update `CLAUDE.md` with the tier table and the plan-before-contract-change gate

### Phase D — Canonical documentation

- Run `/install-documentation` to populate `docs/reference/`
- Add `/doc-audit` and `/doc-sprint-sync` for ongoing maintenance

### Phase E — Drift control

- Add `docs/audits/` with templates and `.claude/rules/workflow-audits.md`
- Run a first mini audit after the first non-trivial feature

### Phase F — Roadmap and compounding

- Add `docs/roadmap/ROADMAP.md` with phases and observable deliverables
- Add `docs/solutions/` and `/compound` for knowledge compounding

### Phase G — Project-specific extensions

- Create 3-6 domain skills for your stack
- Add your own `.claude/agents/`, hooks, or `settings.json` if you want them — FBS ships none and depends on none
