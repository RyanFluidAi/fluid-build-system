---
doc_type: abs_builders_quickstart
status: active
created: 2026-01-25
updated: 2026-02-25
---

# ABS for Builders (Non-Coders)

ABS (Agent Build System) makes it possible to build confidently with AI agents by keeping the project organized and verifiable.

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

- `/verify-install` — confirms ABS is installed correctly
- `/start-session` — reviews project status at the start of a session
- `/pre-flight-git` — prepares repo for new work (clean state, sync, prune)
- `/new-idea` — explores a new feature or concept
- `/review-idea-doc` — validates an idea doc before progressing to a plan
- `/new-plan` — creates a plan (approval gate for contract changes)
- `/review-plan-doc` — validates a plan for completeness and consistency
- `/check-plan` — deep review of plan feasibility against the codebase
- `/new-sprint` — creates a sprint plan (with parallel sub-agent task tags)
- `/review-sprint-doc` — validates a sprint doc before implementation
- `/start-sprint` — begins executing the sprint (parallel sub-agents by domain)
- `/check-sprint` — deep code review after implementation, before verification
- `/review-sprint` — verification gates + close-out (`--deep` for parallel reviewers)
- `/mini-audit` — creates a quick check document after non-trivial work
- `/post-merge-git` — cleans up after a PR merge
- `/close-session` — end-of-session checklist to keep the repo "resume fast"

## The governance pipeline (how features progress)

```
idea → review → plan → review → check → approval → sprint → review → build → check → close
```

1. Explore with `/new-idea`
2. Validate idea with `/review-idea-doc`
3. Formalize with `/new-plan` (if schema/API/DB changes needed)
4. Validate plan with `/review-plan-doc`
5. Check feasibility with `/check-plan`
6. Get approval
7. Create sprint with `/new-sprint`
8. Validate sprint with `/review-sprint-doc`
9. Execute with `/start-sprint`
10. Code review with `/check-sprint`
11. Close out with `/review-sprint`

## Install Documentation (one-time)

When ABS is first installed into a repo, you install the canonical documentation set once:

- Start with `docs/reference/PLATFORM_OVERVIEW_CANONICAL.md`
- End with `docs/reference/GLOBAL_TERMINOLOGY_INDEX_CANONICAL.md`

This establishes the source-of-truth docs so future features can be documented correctly.
