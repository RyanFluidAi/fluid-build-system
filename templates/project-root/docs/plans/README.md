# Plans

Formal plan documents for contract-impacting changes. Plans require explicit approval before implementation.

## When a plan is required

- Database schema changes (tables, columns, constraints, migrations)
- API contract changes (endpoints, request/response shapes, error envelopes)
- Integration contract changes (external payloads, webhooks, partner APIs)
- Canonical business rule changes

## When a plan is NOT required

- Internal refactors with no contract change
- Bug fixes restoring compliance with existing specs

## What a plan is

A plan records a decision that has **already been made**. It states one direction as fact, in enough detail to build from.

- **No alternatives.** Options are weighed in `/new-idea` and stay in the idea doc. The plan template has no "Alternatives Considered" section by design.
- **No hedging.** "The table gains a column", not "we could add a column". `/check-plan` flags hedging language as P0.
- **One plan, one sprint.** If the work won't fit in one sprint, split it into several plans, each independently approvable.

## Workflow

1. Create plan via `/new-plan` — reports the assigned `PLAN-NNN`
2. (Optional) Deep-review via `/check-plan` — feasibility and plan discipline
3. Get explicit approval
4. Create the single sprint via `/new-sprint`
5. Implement, close via `/review-sprint`, then optionally refresh canonical docs via `/doc-sprint-sync`
6. Mark plan `status: implemented` and record the sprint number

## Naming convention

Files use sequential numbering: `PLAN-NNN-YYYY-MM-DD-<topic>.md` (e.g., `PLAN-001-2026-03-13-auth-schema-changes.md`). The `/new-plan` skill assigns the next available number automatically.

## Template

See `plan-template.md` in this directory.
