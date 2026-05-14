---
doc_type: fbs_builders_quickstart
status: active
created: 2026-01-25
updated: 2026-05-13
---

# FBS for Builders (Non-Coders)

FBS (Fluid Build System) makes it possible to build confidently with AI agents by keeping the project organized and verifiable.

## The 5 things (simple language)

- **Rules**: permanent guardrails the AI must follow
  Location: `CLAUDE.md` + `.claude/rules/`
- **Playbooks**: small "how-to" guides the AI loads only when needed
  Location: `.claude/skills/`
- **Plans**: approval documents for contract changes (schema/API/DB/business rules)
  Location: `docs/plans/`
- **Sprints**: what we're building now
  Location: `docs/sprints/`
- **Checks**: audits that confirm we didn't ship undocumented or drifting behavior
  Location: `docs/audits/`

## The 3 docs you always keep current

- `PROJECT_STATUS.md` — the fastest resume snapshot
- `docs/sprints/CURRENT_STATUS.md` — what's happening now
- `docs/sprints/<active sprint>.md` — the plan you're executing

## The skills you'll use most (type `/` in Claude Code)

- `/verify-install` — confirms FBS is installed correctly
- `/start-session` — reviews project status at the start of a session
- `/new-idea` — explores a new feature or concept
- `/review-idea-doc` — validates an idea doc before progressing to a plan
- `/new-plan` — creates a plan (approval gate for contract changes)
- `/check-plan` — deep review of plan feasibility against the codebase
- `/new-sprint` — creates a sprint plan (self-validates; parallel sub-agent task tags)
- `/start-sprint` — begins executing the sprint (parallel sub-agents by domain)
- `/check-sprint` — deep code review after implementation, before verification
- `/review-sprint` — verification gates + close-out (`--deep` for parallel reviewers)
- `/sync` — syncs your local repo with remote (fetch, pull, prune)
- `/mini-audit` — creates a quick check document after non-trivial work
- `/doc-audit` — gap report comparing the codebase to canonical docs
- `/doc-sprint-sync` — (optional, post-sprint) refresh canonical docs to match what was built
- `/close-session` — end-of-session checklist (commits, status updates, session log)

## The governance pipeline (how features progress)

```
idea → plan → check → approval → sprint → build → check → close
```

1. Explore with `/new-idea`
2. Validate idea with `/review-idea-doc`
3. Formalize with `/new-plan` (if schema/API/DB changes needed)
4. Check feasibility with `/check-plan`
5. Get approval
6. Create sprint with `/new-sprint` (self-validates)
7. Execute with `/start-sprint`
8. Code review with `/check-sprint`
9. Close out with `/review-sprint`
10. (Optional) Refresh canonical docs with `/doc-sprint-sync`

## Install Documentation (one-time)

When FBS is first installed into a repo, run `/install-documentation` once. This launches sub-agents that explore the codebase and create comprehensive canonical docs under `docs/reference/` based on what actually exists in the repo.

Run `/verify-install` afterward to confirm all canonical docs are populated (not just placeholder templates).

## Keeping canonical docs current

Canonical doc updates are **decoupled from sprint close** — sprints are not blocked on documentation. Two skills handle ongoing maintenance:

- `/doc-audit` — scans the codebase against existing canonical docs and produces a prioritised gap report
- `/doc-sprint-sync` — manual, owner-triggered refresh that updates canonical docs to reflect what a sprint just built

Run them when worth doing, not as a sprint-close gate.
