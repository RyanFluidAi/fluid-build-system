---
doc_type: fbs_builders_quickstart
status: active
created: 2026-01-25
updated: 2026-02-25
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
- `/review-plan-doc` — validates a plan for completeness and consistency
- `/new-sprint` — creates a sprint plan (with parallel sub-agent task tags)
- `/review-sprint-doc` — validates a sprint doc before implementation
- `/start-sprint` — begins executing the sprint (parallel sub-agents by domain)
- `/check-sprint` — deep code review after implementation, before verification
- `/review-sprint` — verification gates + close-out (`--deep` for parallel reviewers)
- `/commit` — creates a structured git commit with a clear message
- `/sync` — syncs your local repo with remote (fetch, pull, prune)
- `/mini-audit` — creates a quick check document after non-trivial work
- `/close-session` — end-of-session checklist to keep the repo "resume fast"

## The governance pipeline (how features progress)

```
idea → review → plan → review → check → approval → sprint → review → build → check → close
```

1. Explore with `/new-idea`
2. Validate idea with `/review-idea-doc`
3. Formalize with `/new-plan` (if schema/API/DB changes needed)
4. Check feasibility with `/check-plan`
5. Validate plan with `/review-plan-doc`
6. Get approval
7. Create sprint with `/new-sprint`
8. Validate sprint with `/review-sprint-doc`
9. Execute with `/start-sprint`
10. Code review with `/check-sprint`
11. Close out with `/review-sprint`

## Install Documentation (one-time)

When FBS is first installed into a repo, run `/install-documentation` once. This launches sub-agents that explore the codebase and create comprehensive canonical docs under `docs/reference/` based on what actually exists in the repo.

Run `/verify-install` afterward to confirm all canonical docs are populated (not just placeholder templates).
