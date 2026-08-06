---
doc_type: fluid_build_system
status: active
created: 2026-01-25
updated: 2026-08-06
---

# FBS — Fluid Build System (Claude Code)

A **portable starter kit** for setting up the **FBS (Fluid Build System)**, so AI agents (and humans) can build confidently with:

- **Progressive disclosure** — skills loaded on demand, not giant rules
- **Three tiers of ceremony** — pick the smallest one that fits the work
- **One approval gate** — contract changes need a plan; everything else is a default
- **One-source-of-truth status** — fast resume across sessions
- **Parallel execution** — sprints declare workstreams that sub-agents run concurrently
- **Drift control** — mini + in-depth audits
- **Decoupled documentation** — canonical docs maintained out-of-band, never a sprint-close gate

FBS ships **no hooks, no custom agent definitions, and no settings file**. It is documentation and skills only — nothing that intercepts your tool calls or fights your permissions setup.

---

## How it works

### Pick a tier

Most work is not a full pipeline. Choose the smallest tier that fits.

| Tier | When | Path |
|------|------|------|
| **1 — Small fix** | 1-3 files, no contract change | Just do it. No docs. |
| **2 — Sprint** | Multi-file feature or refactor, no contract change | `/new-sprint` → `/start-sprint` → `/review-sprint` |
| **3 — Plan + sprint** | Changes a schema, API shape, integration payload, or documented business rule | `/new-idea` → `/new-plan` → ⏸ approval → `/new-sprint` → `/start-sprint` → `/review-sprint` |

**The one hard gate:** contract changes need an approved plan in `docs/plans/` before implementation. Everything else is a default you can override.

Optional checks, run when the work warrants it:

| Check | Runs after | Purpose |
|-------|-----------|---------|
| `/review-idea-doc` | `/new-idea` | Confirms the idea settled on one direction |
| `/check-plan` | `/new-plan` | Feasibility against the real codebase |
| `/check-sprint` | `/start-sprint` | Deep code review of the diff |
| `/doc-sprint-sync` | `/review-sprint` | Refresh canonical docs |

### Idea → plan → sprint

The three document types have strictly separated jobs:

- **Idea** — explores alternatives. Ends with one chosen direction and the rejected options recorded.
- **Plan** — states one decision as fact. No alternatives, no hedging. Produces **exactly one sprint**. Work too big for one sprint becomes multiple plans.
- **Sprint** — the executable work plan. Declares **parallel workstreams** owning disjoint file sets, so concurrent sub-agents can run them.

Every idea, plan, and sprint gets a globally sequential number, and the skill that creates one reports that number and path in its final message.

The links are bidirectional and machine-checkable: a sprint's `plan:` frontmatter points up, the plan's Implementation Record points down, and `/new-sprint` refuses to create a second sprint against a plan that already has one.

### Roadmap

If the project keeps a `docs/roadmap/ROADMAP.md`, the sprint lifecycle maintains it: `/new-plan` and `/new-sprint` register themselves against a phase, and `/review-sprint` ticks that phase's deliverables and advances its status on close. Delete `docs/roadmap/` if you don't want one — the skills skip their roadmap steps when it's absent.

### Sprint stages

```
planning → in_progress → verification → done
```

- `/start-sprint` moves `planning → in_progress`
- `/review-sprint` moves `verification → done` (the only way to close a sprint)

Multiple sprints may be open at once. `/start-sprint` warns if others are open but never blocks.

---

## What's included

### 17 skills (type `/` in Claude Code)

**Governance pipeline:**

| Skill | Purpose |
|-------|---------|
| `/new-idea` | Explore a concept, weigh alternatives, land on one direction |
| `/review-idea-doc` | Confirm the idea is decided and ready to plan |
| `/new-plan` | Write the authoritative spec for one contract change |
| `/check-plan` | Feasibility review + plan-discipline checks |
| `/new-sprint` | Create the executable work plan with parallel workstreams |
| `/start-sprint` | Execute it, delegating tracks to concurrent sub-agents |
| `/check-sprint` | Deep code review of the diff |
| `/review-sprint` | Verification gates + close-out |

**Documentation:** `/install-documentation`, `/doc-audit`, `/doc-sprint-sync`, `/doc-write` (internal)

**Audits:** `/mini-audit`, `/in-depth-audit`

**Utility:** `/sync`, `/compound`, `/verify-install`

### 2 rules

Both are path-scoped, so they load only when Claude touches matching files:

- `workflow-sprints.md` (`docs/sprints/**`) — stage tracking, concurrent-sprint policy, definition of done
- `workflow-audits.md` (`docs/audits/**`) — drift detection and remediation

Everything that must always be in context lives in `CLAUDE.md`.

### No agents, no hooks

FBS deliberately ships neither. Parallel work uses general-purpose sub-agents driven by the sprint doc's workstream table — no custom agent definitions to maintain, no bundled reviewers that drift out of date. Add your own if you want them; FBS won't overwrite them.

---

## Getting started

### Docs (recommended reading order)

1. `BUILDERS_QUICKSTART.md` — simple mental model and the skills you'll use
2. `INSTALLATION_CHECKLIST.md` — step-by-step install into a repo
3. `SYSTEM_GUIDE.md` — deeper reference for extending FBS

### Quick install (new repo)

1. Copy everything under `templates/project-root/` into your repo root
2. Edit `CLAUDE.md` with your project name, tech stack, conventions
3. Edit `PROJECT_STATUS.md` with current project state
4. Edit `docs/sprints/CURRENT_STATUS.md` with active work (or "none yet")
5. Run `/install-documentation` to create canonical docs from your codebase
6. Run `/verify-install` to confirm everything is in place

### Adopting into an existing repo

| Phase | What to add | Purpose |
|-------|------------|---------|
| **A** | `CLAUDE.md`, `PROJECT_STATUS.md`, `CURRENT_STATUS.md` | Resume fast |
| **B** | `docs/sprints/` + sprint template, `/new-sprint`, `/start-sprint`, `/review-sprint` | Tier 2 work |
| **C** | `docs/plans/`, `docs/ideas/` + templates, `/new-plan`, `/new-idea` | Tier 3 approval gate |
| **D** | `docs/reference/` via `/install-documentation` | Canonical contracts |
| **E** | `docs/audits/` + templates | Drift control |
| **F** | `docs/roadmap/`, `docs/solutions/`, `/compound` | Roadmap tracking and knowledge compounding |

See `SYSTEM_GUIDE.md` for the phased rollout detail.

---

## Design goals

- **Low token** — load only relevant skills and the current status doc, not the repo
- **Low ceremony** — one hard gate, three tiers, nothing that blocks work it shouldn't
- **Unambiguous plans** — one direction, one sprint, no hedging
- **Fast execution** — sprints are designed for concurrent sub-agents from the start
- **Resume fast** — any session picks up from `PROJECT_STATUS.md` and `CURRENT_STATUS.md`

## Upstream compatibility

Check the current Claude Code docs for convention changes:

- [CLAUDE.md and Memory](https://code.claude.com/docs/en/memory)
- [Skills](https://code.claude.com/docs/en/skills)
- [Subagents](https://code.claude.com/docs/en/sub-agents)
- [Settings](https://code.claude.com/docs/en/settings)
