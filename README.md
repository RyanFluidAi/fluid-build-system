---
doc_type: fluid_build_system
status: active
created: 2026-01-25
updated: 2026-05-13
---

# FBS — Fluid Build System (Claude Code)

A **portable starter kit** for setting up the **FBS (Fluid Build System)**, so AI agents (and humans) can build confidently with:

- **Progressive disclosure** — skills loaded on demand, not giant rules
- **One-source-of-truth status** — fast resume across sessions
- **Governance pipeline** — idea → plan → check → approval → sprint → build → check → close
- **Spec-driven execution** — sprints with stage tracking
- **Drift control** — mini + in-depth audits
- **Decoupled documentation** — canonical docs maintained out-of-band via `/doc-audit` and `/doc-sprint-sync` (not gated to sprint close)
- **Session management** — start/close session, knowledge compounding

---

## How it works

### The daily cycle

```
/start-session          ← pick up where you left off
    ↓
  work (small fix  OR  full governance pipeline)
    ↓
/close-session          ← commit, update status docs, write session log, shut down services
```

### The governance pipeline (building features)

When you're building something significant, work moves through 8 steps:

```
 Step  Skill                What happens
 ───── ──────────────────── ──────────────────────────────────────────
  1    /new-idea             explore the concept, create an idea doc
  2    /review-idea-doc      validate the idea is complete and clear
  3    /new-plan             formalize contract changes into a plan
  4    /check-plan           deep feasibility check against the codebase
  5    ⏸ approval            you approve the plan before any code is written
  6    /new-sprint           break the plan into an executable task list (self-validates)
  7    /start-sprint         build it (parallel sub-agents by domain)
  8    /check-sprint         deep code review of everything that was built
  9    /review-sprint        final verification → stage: done
```

After sprint close, optionally run `/doc-sprint-sync` to refresh canonical docs in `docs/reference/`. Documentation sync is decoupled from sprint close — it's a separate, owner-triggered workflow.

Small fixes (1-3 files, no structural changes) skip the pipeline entirely.

### Sprint stages

```
planning → in_progress → verification → done
```

- `/start-sprint` moves `planning → in_progress`
- `/check-sprint` moves `in_progress → verification`
- `/review-sprint` moves `verification → done` (only way to close a sprint)

---

## What's included

### 21 Skills (type `/` in Claude Code)

**Governance pipeline (in order):**

| Skill | Purpose |
|-------|---------|
| `/new-idea` | Explore a feature concept, create idea doc |
| `/review-idea-doc` | Validate idea completeness before planning |
| `/new-plan` | Create formal plan for contract changes |
| `/check-plan` | Deep feasibility review against codebase |
| `/new-sprint` | Create agent-executable task list (self-validates) |
| `/start-sprint` | Execute the work plan (parallel sub-agents) |
| `/check-sprint` | Deep code review after implementation |
| `/review-sprint` | Final verification + close-out (`--deep` for 6 parallel reviewers) |

**Session management:**

| Skill | Purpose |
|-------|---------|
| `/start-session` | Review project status, sprint, blockers at session start |
| `/close-session` | Commit, write session log, update status docs, shut down services |

**Git workflows:**

| Skill | Purpose |
|-------|---------|
| `/sync` | Sync local repo with remote (fetch, pull, prune) |

**Audits:**

| Skill | Purpose |
|-------|---------|
| `/mini-audit` | Quick drift check after non-trivial changes |
| `/in-depth-audit` | Comprehensive subsystem audit |

**Documentation (decoupled from sprint close):**

| Skill | Purpose |
|-------|---------|
| `/install-documentation` | One-time canonical doc setup (sub-agents explore the codebase) |
| `/doc-audit` | Scan codebase against canonical docs and produce a gap report |
| `/doc-sprint-sync` | Manual post-sprint sync — refresh canonical docs for what changed |
| `/doc-write` | Writing standards (loaded by the doc skills above; not user-invocable) |
| `documentation-governance` | High-level non-negotiables + pointers to the four doc skills |

**Utility:**

| Skill | Purpose |
|-------|---------|
| `/compound` | Document solved problems as reusable solutions |
| `/skill-creator` | Create new skills |
| `/verify-install` | Verify FBS installation completeness |

### 9 Agents

**Execution agents:**
- `verifier` — validates completed work (read-only)
- `test-runner` — runs tests and fixes failures
- `debugger` — root cause analysis and minimal fixes

**Reviewer agents (used by `/review-sprint --deep`):**
- `architecture-reviewer` — module boundaries, dependency direction
- `security-reviewer` — auth gaps, injection vectors, data exposure
- `performance-reviewer` — N+1 queries, missing indexes, re-renders
- `data-integrity-reviewer` — missing transactions, constraint gaps
- `test-quality-reviewer` — coverage gaps, weak assertions
- `docs-governance-reviewer` — undocumented contract changes, stale docs

### 4 Rules

- `foundation.md` — progressive disclosure, governance gates, session checkpoints
- `workflow-sprints.md` — sprint stage tracking and definition of done
- `workflow-small-fixes.md` — low-ceremony guardrails for small changes
- `workflow-audits.md` — drift detection and remediation

### 3 Hooks

- `shell-guard.sh` — blocks risky shell commands (`rm -rf`, `drop table`, `git push --force`)
- `read-guard.sh` — blocks reading secret files (`.env`, credentials)
- `format.sh` — auto-formats files after edits

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
6. Run `/verify-install` to confirm everything is in place (including populated canonical docs)

### Adopting into an existing repo

Do it incrementally:

| Phase | What to add | Purpose |
|-------|------------|---------|
| **A** | `CLAUDE.md`, `PROJECT_STATUS.md`, `CURRENT_STATUS.md` | Resume fast |
| **B** | `docs/plans/`, `docs/ideas/` + templates | Governance gates |
| **C** | `.claude/skills/` (3-6 initial skills) | Progressive disclosure |
| **D** | `docs/sprints/` + sprint template | Execution tracking |
| **E** | `docs/audits/` + audit templates | Drift control |
| **F** | `.claude/agents/`, `.claude/settings.json`, `.mcp.json` | Automation |
| **G** | `/start-session`, `/close-session`, `docs/sessions/`, `docs/solutions/` | Session management |

See `SYSTEM_GUIDE.md` for the phased rollout detail.

---

## Design goals

- **Low token** — don't load the entire repo context; load only relevant skills and the current status doc
- **Stable governance** — contract changes are proposed and approved before implementation
- **High confidence** — audits and fast checks prevent drift and undocumented behavior
- **Resume fast** — any session can pick up where the last one left off via status docs and session logs

## Upstream compatibility

Check the current Claude Code docs for any convention changes:

- [CLAUDE.md and Memory](https://code.claude.com/docs/en/memory)
- [Skills](https://code.claude.com/docs/en/skills)
- [Subagents](https://code.claude.com/docs/en/sub-agents)
- [Hooks](https://code.claude.com/docs/en/hooks)
- [Settings](https://code.claude.com/docs/en/settings)
- [MCP Servers](https://code.claude.com/docs/en/mcp)
