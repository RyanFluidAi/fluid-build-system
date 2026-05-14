---
name: doc-sprint-sync
description: Post-sprint documentation sync. Reads the sprint's changes, identifies which canonical docs need creating or updating, and works through them one at a time. Run manually by the owner after a sprint — it is NOT invoked automatically by /review-sprint.
argument-hint: "[optional: sprint doc path under docs/sprints/]"
---

# /doc-sprint-sync — Sprint Documentation Sync

## Goal

After a sprint's implementation is complete, ensure all canonical documentation under `docs/reference/` is updated to reflect what was built. Work through each document one at a time with full focus.

This is an **optional, manual** step. `/review-sprint` does not invoke it — the owner decides when documentation sync is worth doing.

## Inputs

$ARGUMENTS

If no sprint path is provided:
- Read `docs/sprints/CURRENT_STATUS.md` and use `active_sprint`.

## Process

### 1) Understand what changed

- Read the sprint doc — focus on acceptance criteria, contracts section, and any documentation notes
- Read the git diff for the sprint's commits to see exactly what code changed:
  - Schema changes (new/modified tables, columns, types)
  - API changes (new/modified routes, request/response shapes)
  - Event changes (new/modified event types, payloads)
  - New modules or significant refactors

### 2) Map changes to documentation

For each change, determine:
- Does an existing canonical doc cover this area?
- If yes: does it need updating?
- If no: does a new canonical doc need creating?

Build a work list:

```
| Action | Document | What changed | Source files |
|--------|----------|-------------|--------------|
| Update | docs/reference/SCHEMA_AND_CONTRACTS_CANONICAL.md | Added priority field | <path/to/schema> |
| Create | docs/reference/CHANNELS_CANONICAL.md | New module, no doc exists | <path/to/module> |
```

Order the work list by dependency — if doc B references doc A, do A first.

### 3) Write each document

For each item in the work list:

1. Load the `/doc-write` skill (`.claude/skills/doc-write/SKILL.md`)
2. Read all source files listed for this document
3. Read the existing document (if updating)
4. Write or update the document following doc-write standards
5. Run the self-review checklist from the doc-write skill
6. Fix any issues before moving to the next document

**One document at a time.** Complete each document fully before starting the next.

### 4) Update the documentation inventory

After all documents are done, update `docs/reference/DOCUMENTATION_INVENTORY.md` if any new docs were created or any inventory entries changed status.

### 5) Report results

Produce a summary:

```markdown
## Documentation Sync — Sprint [name]

### Documents updated
- `docs/reference/SCHEMA_AND_CONTRACTS_CANONICAL.md` — added priority field schema, API parameters, events

### Documents created
- `docs/reference/CHANNELS_CANONICAL.md` — new canonical doc for channel transport layer

### Flagged for review
- `docs/reference/AGENTS_CANONICAL.md` — source code ambiguous about new lifecycle, needs manual clarification

### No changes needed
- `docs/reference/AUTH_CANONICAL.md` — no auth changes in this sprint
```

## Rules

- **Don't skip small changes** — a single new field still needs documenting
- **Don't create aspirational docs** — only document what's implemented in code right now
- **Respect existing doc structure** — when updating, preserve the document's existing organisation
- **Flag uncertainty** — if the code is ambiguous, flag it for manual review rather than guessing
