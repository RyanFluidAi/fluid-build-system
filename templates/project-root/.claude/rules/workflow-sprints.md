---
paths: ["docs/sprints/**"]
---

# Workflow: Sprints

Applies when working on sprint-based development or editing sprint docs under `docs/sprints/SPRINTS/`.

## Start checklist (required)

- Identify the sprint doc: `docs/sprints/SPRINTS/<sprint>.md`
- Confirm **stage**: planning -> in_progress -> verification -> done
- Load relevant skills for the domains touched (usually 2-4 max).
- If canonical contracts will change: create a plan in `docs/plans/` and wait for approval.

## Stage tracking (required)

Maintain one source of truth for stage:
- Update sprint doc stage/status fields as you progress.
- Update `docs/sprints/CURRENT_STATUS.md` when the active sprint, priorities, or blockers change.
- Update `PROJECT_STATUS.md` so the repo remains "resume fast".

## Definition of Done (DoD)

- Implementation matches sprint acceptance criteria.
- Required tests/build checks run.
- Contract-impacting changes have approved plans and updated canonical docs.
- Mini audit completed if canonical surfaces were touched.
- Status docs updated (`PROJECT_STATUS.md` and `docs/sprints/CURRENT_STATUS.md` at minimum).
