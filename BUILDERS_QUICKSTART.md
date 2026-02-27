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
- `docs/sprints/SPRINTS/<active sprint>.md` — the plan you're executing

## The 10 skills you'll use most (type `/` in Claude Code)

- `/verify-install` — confirms ABS is installed correctly
- `/start-session` — reviews project status at the start of a session
- `/sync` — syncs local repo with remote (fetch, pull, prune)
- `/new-idea` — explores a new feature or concept
- `/new-plan` — creates a plan (approval gate for contract changes)
- `/new-sprint` — creates a sprint plan
- `/start-sprint` — begins executing the sprint
- `/check-sprint` — deep code review after implementation, before closing
- `/mini-audit` — creates a quick check document after non-trivial work
- `/close-session` — end-of-session checklist to keep the repo "resume fast"

## The governance pipeline (how features progress)

```
idea -> plan (if contract changes) -> sprint -> implementation -> review -> done
```

1. Explore with `/new-idea`
2. Formalize with `/new-plan` (if schema/API/DB changes needed)
3. Get approval
4. Create sprint with `/new-sprint`
5. Execute with `/start-sprint`
6. Code review with `/check-sprint`
7. Close out with `/review-sprint`

## Install Documentation (one-time)

When ABS is first installed into a repo, you install the canonical documentation set once:

- Start with `docs/reference/PLATFORM_OVERVIEW_CANONICAL.md`
- End with `docs/reference/GLOBAL_TERMINOLOGY_INDEX_CANONICAL.md`

This establishes the source-of-truth docs so future features can be documented correctly.
