# Sprints

Sprint documents for spec-driven execution. Each sprint follows a stage progression: planning -> in_progress -> verification -> done.

## Key Files

- `CURRENT_STATUS.md` — what's happening now (overwrite-only snapshot)
- `SPRINT-NNN-*.md` — individual sprint documents, alongside this README
- `sprint-template.md` — template for new sprints

## Parallel workstreams

Every sprint declares tracks that own **disjoint sets of files**. `/start-sprint` launches each concurrent track as its own general-purpose sub-agent, all at once. Two concurrent tracks must never claim the same file — disjoint ownership is what makes the parallelism safe.

## Concurrent sprints

More than one sprint may be open at a time. `/start-sprint` warns if others are still in `planning` or `in_progress`, then continues. The warning is advice, not a lock.

## Naming convention

Files use sequential numbering: `SPRINT-NNN-YYYY-MM-DD-<topic>.md` (e.g., `SPRINT-001-2026-03-13-auth-implementation.md`). The `/new-sprint` skill assigns the next available number automatically.

## Workflow

1. Create sprint via `/new-sprint` — reports the assigned `SPRINT-NNN`
2. Start implementation via `/start-sprint` — runs tracks concurrently
3. (Optional) Deep code review via `/check-sprint` — worth it above ~15 changed files
4. Close sprint via `/review-sprint` — the only skill that sets `stage: done`
5. (Optional, manual) Refresh canonical docs via `/doc-sprint-sync`
