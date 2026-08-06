---
paths: ["docs/sprints/**"]
---

# Workflow: Sprints

Applies when working on sprint-based development or editing sprint docs under `docs/sprints/`.

## Start checklist (required)

- Identify the sprint doc: `docs/sprints/SPRINT-NNN-YYYY-MM-DD-<topic>.md`
- Confirm **stage**: planning -> in_progress -> verification -> done
- Load relevant skills for the domains touched (usually 2-4 max).
- If canonical contracts will change: create a plan in `docs/plans/` and wait for approval.

## Concurrent sprints

More than one sprint may be open at a time. This is allowed and never blocks.

Ideally, an earlier sprint has reached `verification` or `done` before the next one starts. When it hasn't, **warn once and continue** — name the other open sprint(s) and their stages, then proceed. Do not refuse to start work on this basis.

## Stage tracking (required)

Maintain one source of truth for stage:
- Update sprint doc stage/status fields as you progress.
- Update `docs/sprints/CURRENT_STATUS.md` when the active sprint, priorities, or blockers change.
- Update `PROJECT_STATUS.md` so the repo remains "resume fast".

Guardrail:
- `docs/sprints/CURRENT_STATUS.md` is **overwrite-only**. Do not accumulate running history there; history belongs in the sprint doc's Notes section.

## Definition of Done (DoD)

Required to close:
- Implementation matches sprint acceptance criteria.
- Required tests/build checks run.
- Contract-impacting changes have approved plans.
- Status docs updated (`PROJECT_STATUS.md` and `docs/sprints/CURRENT_STATUS.md` at minimum).
- Plan Implementation Record filled and roadmap phase advanced, where either applies.

Recommended, not gating — surface as a reminder at close, don't block on them:
- Mini audit, if canonical surfaces were touched (`/mini-audit`).
- Canonical doc refresh, if contracts changed (`/doc-sprint-sync`).
