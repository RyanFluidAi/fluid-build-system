---
project: your-project-name
status: active
---

# Agent Operating Manual

This repository uses the **Fluid Build System (FBS)** — a lightweight governance framework for AI-assisted development: progressive disclosure, one approval gate, and drift control.

## Resume fast (read in order)

1. `PROJECT_STATUS.md` — fastest snapshot of current state
2. `docs/sprints/CURRENT_STATUS.md` — what's happening now
3. `docs/audits/active/` — unresolved P0/P1 drift findings
4. `docs/roadmap/ROADMAP.md` — which phase the current work advances (if the project uses one)
5. `.claude/skills/` — load only the relevant skills (2-4 max)

## Pick a tier before you start

Most work is not a full pipeline. Choose the smallest tier that fits and say which one you picked.

| Tier | When | Path |
|------|------|------|
| **1 — Small fix** | 1-3 files, no contract change | Just do it. No docs. |
| **2 — Sprint** | Multi-file feature or refactor, **no** contract change | `/new-sprint` → `/start-sprint` → `/review-sprint` |
| **3 — Plan + sprint** | Changes a **contract**: schema, API shape, integration payload, or documented business rule | `/new-idea` (optional) → `/new-plan` → ⏸ approval → `/new-sprint` → `/start-sprint` → `/review-sprint` |

**The one non-negotiable gate:** if a change impacts a canonical contract, a plan must exist in `docs/plans/` and be explicitly approved before implementation. Everything else is a default you can override by saying so.

Optional checks, run when the work warrants it — not required:
- `/review-idea-doc` after `/new-idea`
- `/check-plan` before approving a plan
- `/check-sprint` after implementation, before `/review-sprint`
- `/doc-sprint-sync` after a sprint that changed contracts

### Tier 1 guardrails (small fixes)

- Keep diffs small and localized; prefer existing patterns.
- Don't create documentation for a one-off fix.
- If it turns out to touch a contract, stop and switch to Tier 3.
- Finish by stating what changed (files) and how you verified it.

## Sprint stages

```
planning → in_progress → verification → done
```

`/start-sprint` moves planning → in_progress. `/review-sprint` moves verification → done and is the only skill that should set `stage: done`.

Multiple sprints may be open at once. Starting a new one while another is open is allowed — `/start-sprint` will warn, not block.

## Document numbering

Ideas, plans, and sprints use globally sequential, zero-padded numbers. Scan the directory for the next available number.

- `docs/ideas/IDEA-NNN-YYYY-MM-DD-<topic>.md`
- `docs/plans/PLAN-NNN-YYYY-MM-DD-<topic>.md`
- `docs/sprints/SPRINT-NNN-YYYY-MM-DD-<topic>.md`

The `number:` field in frontmatter must match the filename. Whenever you create one of these documents, **state the assigned number and full path in your final message.**

## One plan, one sprint

A plan describes exactly one direction and produces exactly one sprint. If the work won't fit in one sprint, the plan is too big — split it into multiple plans, each with its own sprint.

Plans are authoritative and directive. They state what will be built, not what might be. Alternatives belong in idea docs, not plans.

## Progressive disclosure

- Load only what you need. Don't read the entire repo.
- Skills load on demand — descriptions are always in context, full content loads when invoked.
- Prefer 2-4 relevant skills over broad repo-wide reading.

## Session checkpoints

At the start of meaningful work, state which status docs you read, which skill(s) you loaded, and which tier you picked.

Before you stop, update `PROJECT_STATUS.md` and `docs/sprints/CURRENT_STATUS.md` if priorities, the active sprint, or blockers changed.

## Documentation

Canonical docs live in `docs/reference/` and are maintained out-of-band — they are **not** a sprint-close gate.

- `/install-documentation` (one-time) — establishes the canonical set from the codebase
- `/doc-audit` (on demand) — gap report between codebase and canonical docs
- `/doc-sprint-sync` (manual, post-sprint) — refresh canonical docs for what a sprint built
- `/doc-write` (internal) — writing standards, loaded by the skills above

Keep documentation minimal: don't create new docs for one-off fixes; prefer updating `PROJECT_STATUS.md` and existing sprint/audit docs.

## Sub-agents

This project ships no custom agent definitions. When a sprint has independent workstreams, delegate them to parallel general-purpose sub-agents as described in the sprint doc's **Parallel Workstreams** section.
