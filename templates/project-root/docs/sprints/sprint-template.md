---
doc_type: sprint
number: NNN
status: active
stage: planning
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
dates:
  start: YYYY-MM-DD
  end: YYYY-MM-DD
sprint_goal: ""
plan: ""
roadmap_phase: ""
---

# SPRINT-NNN: <Name>

## Summary

<!-- One line: what this sprint delivers. Stage must match frontmatter. -->

## Acceptance Criteria

<!-- 5-12 observable outcomes. What must be true at the end. -->
- [ ]

## Contracts / Governance

- Contract-impacting changes expected: Yes/No
- Plan: <!-- exactly one, or "none — Tier 2 implementation-only" -->
- Approval status:
- Roadmap phase: <!-- or "none" -->

## Parallel Workstreams

<!--
Tracks that touch DISJOINT file sets can run concurrently, one sub-agent each.
No two concurrent tracks may list the same file. If the work can't be split,
say so and give a single track — don't invent parallelism.
-->

| Track | Domains | Owns (files/dirs) | Depends on | Done when |
|-------|---------|-------------------|------------|-----------|
| A — | | | — | |
| B — | | | | |

**Execution order**: <!-- e.g. "A first; B and C concurrently after A; D last." -->

## Work Plan

<!-- task | [domain] | [priority] | estimate | track -->

### To Do

### In Progress

### Done

## Test Plan

### Automated
```bash
# lint, typecheck, test, build
```
<!-- Note what each command covers. -->

### Manual
- [ ]

## Review Gate

- [ ] All acceptance criteria met
- [ ] All automated tests pass
- [ ] Manual checklist complete

## Audit Plan

<!-- Mini audit, in-depth, or "none needed" with rationale. Recommended, not gating. -->

## Notes

<!-- Decisions, trade-offs, and pitfalls recorded during implementation. -->
