---
doc_type: fluid_build_system_guide
status: active
created: 2026-01-25
updated: 2026-02-25
---

# FBS — Fluid Build System — Setup Guide (Portable)

This guide explains the **FBS (Fluid Build System)** and how to reproduce it in a different project so an AI agent (in Claude Code) can build confidently with clear governance and low drift.

## Upstream compatibility note

During installation, check the current Claude Code docs for any changes since this kit was created:

- [CLAUDE.md and Memory](https://code.claude.com/docs/en/memory)
- [Skills](https://code.claude.com/docs/en/skills)
- [Subagents](https://code.claude.com/docs/en/sub-agents)
- [Hooks](https://code.claude.com/docs/en/hooks)
- [Settings](https://code.claude.com/docs/en/settings)
- [MCP Servers](https://code.claude.com/docs/en/mcp)

---

## 1) What FBS is

FBS is a **documentation + governance architecture** for AI-assisted development:

- **AI should not "infer" contracts** (schemas, API shapes, business rules). Those are **written canonically**.
- **AI should not load everything**. It should load a small set of **high-signal documents** and the **relevant skills**.
- **Work should be tracked** (current status + sprint doc), and drift should be caught early via **audits + fast checks**.

FBS is built from five pillars:

1. **Rules** (`.claude/rules/`): short workflow guardrails.
2. **Skills** (`.claude/skills/`): progressive-disclosure guidance by domain.
3. **Plans** (`docs/plans/`): approval workflow for contract-impacting changes.
4. **Sprints** (`docs/sprints/`): spec-driven execution and stage tracking.
5. **Audits** (`docs/audits/`): drift detection + remediation tracking.

---

## 2) System structure

### 2.1 The "resume fast" path

**In order**:

- `CLAUDE.md`: project instructions + governance rules (always loaded).
- `PROJECT_STATUS.md`: single-page resume context snapshot.
- `docs/sprints/CURRENT_STATUS.md`: what's happening now (active sprint, blockers, stage).
- `.claude/skills/`: load only relevant skills.
- `docs/audits/active/`: unresolved drift findings.

### 2.2 The execution loop (how features get built)

**Governance pipeline** (11 steps):

```
 Step  Skill                Purpose
 ───── ──────────────────── ──────────────────────────────────────────
  1    /new-idea             explore architecture and design
  2    /review-idea-doc      validate idea completeness
  3    /new-plan             formalize contract changes
  4    /review-plan-doc      validate plan format and completeness
  5    /check-plan           deep feasibility review against codebase
  6    ⏸ approval            wait for explicit user approval
  7    /new-sprint           create agent-executable work plan
  8    /review-sprint-doc    validate sprint doc before building
  9    /start-sprint         execute the work plan (parallel sub-agents)
 10    /check-sprint         deep code review after implementation
 11    /review-sprint        verification + close-out → stage: done
```

Every **create** step has a corresponding **review/check** step:

| Create | Format review | Deep review |
|--------|--------------|-------------|
| `/new-idea` | `/review-idea-doc` | — |
| `/new-plan` | `/review-plan-doc` | `/check-plan` |
| `/new-sprint` | `/review-sprint-doc` | — |
| `/start-sprint` (build) | — | `/check-sprint` |
| — | — | `/review-sprint` (final gate) |

**Small fixes** skip the pipeline — follow `.claude/rules/workflow-small-fixes.md`.

### 2.3 Governance gates

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
6. **Session logs**: `docs/sessions/`
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

Governance docs (rules for all other canonical docs):
- `docs/reference/DOCUMENTATION_STANDARDS_CANONICAL.md`
- `docs/reference/DOCUMENTATION_HIERARCHY_CANONICAL.md`

Content docs (populated by sub-agents from the actual codebase):
1. `docs/reference/PLATFORM_OVERVIEW_CANONICAL.md`
2. `docs/reference/SCHEMA_AND_CONTRACTS_CANONICAL.md`
3. `docs/reference/GLOBAL_TERMINOLOGY_INDEX_CANONICAL.md`
4. `docs/reference/DOCUMENTATION_INVENTORY.md`

### 4.3 "No invention" rule

Document what exists. Don't silently introduce new fields, behaviors, or contracts. Gaps follow the plan/approval workflow.

### 4.4 Verification

`/verify-install` checks whether canonical docs are populated or still contain placeholder markers (`YYYY-MM-DD`, bracket placeholders, ellipsis-only sections). Unpopulated docs are flagged with a prompt to re-run `/install-documentation`.

---

## 5) Ongoing Documentation (per sprint)

### 5.1 Required skill

Use the `documentation-governance` skill when updating canonical docs. It is configured as `user-invocable: false` so Claude loads it automatically when relevant.

### 5.2 Sprint Definition of Done

Before sprint stage moves to `done`:
- new behavior is documented
- new/changed schema/API/DB is documented with schema+example+semantics
- global terminology index is updated when new terms are introduced

### 5.3 Audit role

Mini and in-depth audits include a documentation completeness check.

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

Rules live in `.claude/rules/` as plain Markdown files. Unlike Cursor rules, Claude Code rules don't use `alwaysApply` or `globs` frontmatter — they are discovered as project context.

This kit includes four rules:
- `foundation.md` — always-on context loading, progressive disclosure, non-negotiable governance
- `workflow-small-fixes.md` — low-ceremony guardrails for small changes
- `workflow-sprints.md` — spec-driven execution with stage tracking
- `workflow-audits.md` — drift detection and remediation workflow

Foundation rules are always in context (no `paths` restriction). Workflow rules are scoped to their respective doc paths.

### 6.3 Skills

Skills are the primary extension mechanism. Each skill is a directory under `.claude/skills/` containing a `SKILL.md` file.

**Discovery**: Claude loads skill descriptions into context at startup. Full content loads only when a skill is invoked (by user or Claude).

**Frontmatter fields**:

| Field | Purpose |
|-------|---------|
| `name` | Skill identifier (matches the `/name` invocation) |
| `description` | What it does and when to use it (primary trigger mechanism) |
| `argument-hint` | Autocomplete hint, e.g. `[sprint doc path]` |
| `disable-model-invocation` | `true` = only user can invoke via `/name` |
| `user-invocable` | `false` = hidden from `/` menu, Claude auto-loads when relevant |
| `context` | `fork` = runs in isolated subagent context |
| `agent` | Which subagent type when `context: fork` |
| `allowed-tools` | Auto-approve specific tools when this skill is active |

**Included skills** (26 total):

Governance pipeline (in order):
- `new-idea` — explore architecture, create idea artifact
- `review-idea-doc` — validate idea before it becomes a plan
- `new-plan` — formal contract-change plan (with best practices and traceability)
- `review-plan-doc` — validate plan format and completeness before approval
- `check-plan` — deep feasibility review of plan against codebase reality
- `new-sprint` — create agent-executable sprint doc (with parallel sub-agent task tags)
- `review-sprint-doc` — validate sprint doc before implementation
- `start-sprint` — execute work plan (supports parallel sub-agents by domain)
- `check-sprint` — deep code review of sprint changes before verification
- `review-sprint` — verification gates + close-out (`--deep` for parallel reviewer subagents)

Session management:
- `start-session` — review project status at session start
- `close-session` — end-of-session resume-fast checklist (with service shutdown)

Audits:
- `mini-audit` — targeted drift check
- `in-depth-audit` — comprehensive subsystem audit

Documentation:
- `documentation-governance` — canonical doc update rules (background knowledge)
- `install-documentation` — one-time canonical doc setup

Code quality:
- `commit` — structured git commit with conventional message format
- `review-code` — code review for quality, correctness, and conventions

Git workflows:
- `sync` — sync local repo with remote (fetch, pull, prune)

Utility:
- `deploy-app` — deployment workflow
- `compound` — document solved problems
- `skill-creator` — meta-skill for creating new skills
- `create-sub-agent` — interactive wizard for creating custom subagents
- `verify-install` — verify FBS installation

### 6.4 Agents (subagents)

Agents are specialized Claude instances with their own system prompts, tool restrictions, and model selection. Each is a Markdown file in `.claude/agents/`.

**Frontmatter fields**:

| Field | Purpose |
|-------|---------|
| `name` | Unique identifier |
| `description` | What it does and when to delegate |
| `model` | `haiku`, `sonnet`, `opus`, or `inherit` |
| `tools` | Comma-separated tool list (e.g. `Read, Glob, Grep, Bash`) |
| `maxTurns` | Maximum agentic turns |
| `memory` | Persistent memory scope: `user`, `project`, or `local` |
| `permissionMode` | `default`, `acceptEdits`, `dontAsk`, `bypassPermissions`, `plan` |
| `skills` | Skills to preload into agent context |
| `isolation` | `worktree` for git worktree isolation |
| `background` | `true` to always run as background task |

**Included agents** (9 total):

Execution agents:
- `verifier` — validates completed work (read-only)
- `test-runner` — runs tests and fixes failures
- `debugger` — root cause analysis and minimal fixes

Reviewer agents (used by `/review-sprint --deep`):
- `architecture-reviewer` — module boundaries, dependency direction, pattern consistency
- `security-reviewer` — auth gaps, injection vectors, data exposure
- `performance-reviewer` — N+1 queries, missing indexes, unnecessary re-renders
- `data-integrity-reviewer` — missing transactions, constraint gaps, enum mismatches
- `test-quality-reviewer` — coverage gaps, weak assertions, test isolation
- `docs-governance-reviewer` — undocumented contract changes, stale canonical docs

### 6.5 Hooks

Hooks are deterministic automation — shell commands or LLM evaluators that run at specific lifecycle points. Configured in `.claude/settings.json`.

**Key hook events**:

| Event | When | Use case |
|-------|------|----------|
| `PreToolUse` | Before a tool call | Block risky commands, protect secret files |
| `PostToolUse` | After a tool call | Auto-format edited files |
| `SessionStart` | Session starts/resumes | Re-inject context after compaction |
| `Stop` | Claude finishes responding | Verify task completion |

**Three hook types**:
- `command` — run a shell command
- `prompt` — single-turn LLM evaluation (returns ok/not ok)
- `agent` — multi-turn LLM verification with tool access

**Included hooks**:
- `shell-guard.sh` — block risky shell commands (PreToolUse)
- `read-guard.sh` — block reading secret files (PreToolUse)
- `format.sh` — auto-format after edits (PostToolUse)
- Compaction re-injection (SessionStart)
- Status doc update reminder (Stop)

### 6.6 Settings

`.claude/settings.json` configures hooks, permissions, and environment variables.

```json
{
  "permissions": {
    "allow": ["Read", "Glob", "Grep"],
    "deny": []
  },
  "hooks": { ... },
  "env": { ... }
}
```

Personal overrides go in `.claude/settings.local.json` (gitignored).

### 6.7 MCP Servers

`.mcp.json` at the project root configures shared MCP (Model Context Protocol) server connections. Supports environment variable expansion.

---

## 7) Plans: governance gate for contract changes

### 7.1 When plans are required

- schemas, API contracts, integration contracts, business rules

Not required for:
- purely internal refactors
- bug fixes restoring compliance with canonical spec

### 7.2 Plan workflow

1. Explore via `/new-idea`
2. Validate idea via `/review-idea-doc`
3. Create plan via `/new-plan`
4. Validate plan format via `/review-plan-doc`
5. Check feasibility via `/check-plan`
6. Wait for explicit user approval
7. Create sprint via `/new-sprint`
8. Validate sprint doc via `/review-sprint-doc`
9. Implement via `/start-sprint` and update canonical docs
10. Code review via `/check-sprint`
11. Verify and close via `/review-sprint`
12. Mark plan as "Implemented"

---

## 8) Sprints: spec-driven execution

### 8.1 Current status

`docs/sprints/CURRENT_STATUS.md` is the single source of truth for what sprint is active, its stage, and blockers. Update it whenever priorities or blockers change.

### 8.2 Sprint docs

Required fields: stage (`planning | in_progress | verification | done`), acceptance criteria, test plan, links to plans (if contracts change).

### 8.3 Stage progression

```
planning -> in_progress -> verification -> done
```

- `/start-sprint` handles planning -> in_progress
- `/check-sprint` performs deep code review after implementation, before verification
- `/review-sprint` handles verification -> done (the only way to reach done)

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

## 10) Session management

### 10.1 Starting a session

Use `/start-session` at the beginning of every work session. It reads the status docs and gives you a briefing:

1. Reads `PROJECT_STATUS.md` (active work, latest session log, architectural decisions)
2. Reads `docs/sprints/CURRENT_STATUS.md` (active sprint, stage, blockers)
3. Checks `docs/audits/active/` for unresolved P0/P1 issues
4. Summarizes everything in chat: current sprint, priorities, blockers, suggested next steps

This is the "resume fast" entry point — any session can pick up where the last one left off.

### 10.2 Closing a session

Use `/close-session` at the end of every work session. It ensures the repo is ready for the next session:

1. Commits and pushes any uncommitted changes
2. Confirms which workflow was used (small fix, sprint, or audit)
3. Writes a session log to `docs/sessions/YYYY-MM-DD.md` (append-only, 1-10 bullets)
4. Updates `PROJECT_STATUS.md` if priorities, sprint, decisions, or audit counts changed
5. Updates `docs/sprints/CURRENT_STATUS.md` if sprint stage or blockers changed
6. Checks if contracts changed and ensures plans exist
7. Checks if roadmap needs updating
8. Considers whether a new skill should be created or an existing one updated
9. Adds a session summary to the relevant sprint or audit doc
10. Shuts down any running dev servers or services

### 10.3 Session log format

Session logs go in `docs/sessions/YYYY-MM-DD.md` (one file per day, append-only):

- **Summary** (1-5 bullets)
- **Closed / shipped** (0-5 bullets)
- **Decisions / notes** (0-5 bullets)
- **Next / blockers** (0-5 bullets)

### 10.4 How sessions connect to sprints

`/start-session` reads the sprint state. `/close-session` updates it. The session log links to the relevant sprint doc. This creates a traceable history: sprint docs track *what* was planned, session logs track *when* it happened.

---

## 11) Knowledge compounding

### 11.1 Solutions library

Use `/compound` after solving a non-obvious problem to create a solution entry under `docs/solutions/` with:
- Symptom, root cause, fix, prevention step
- Solutions that become reusable procedures are promoted to skills

### 11.2 Skill creation

Use `/skill-creator` (or the skill-creator reference docs) when creating new skills. Follow progressive disclosure: keep SKILL.md under 500 lines, move detailed reference material to supporting files.

---

## 12) Installing into an existing project

### Phase A — Minimal "resume fast"

- Add `CLAUDE.md` to the repo root
- Add `PROJECT_STATUS.md`
- Add `docs/sprints/CURRENT_STATUS.md`

### Phase B — Governance gates

- Add `docs/plans/` with plan template
- Add `docs/ideas/` with idea template
- Update `CLAUDE.md` to require plans for contract-impacting changes

### Phase C — Skills

- Add `.claude/skills/documentation-governance/`
- Create 3-6 initial domain skills

### Phase D — Execution tracking

- Add `docs/sprints/` with sprint template and `CURRENT_STATUS.md`
- Start tracking work using sprint docs with stage progression

### Phase E — Drift control

- Add `docs/audits/` with templates
- Run a first mini audit after the first non-trivial feature

### Phase F — Automation

- Add `.claude/agents/` for verification/debug/testing workflows
- Add `.claude/settings.json` with hooks for formatting and safety gates
- Add `.mcp.json` for shared tool integrations

### Phase G — Session management

- Add `/start-session` and `/close-session` skills
- Add `docs/sessions/` for session logs
- Add `docs/solutions/` for knowledge compounding
