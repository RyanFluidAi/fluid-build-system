---
name: review-sprint
description: Verify a sprint and close it (tests + docs + skills gates). The only workflow that should move a sprint to stage done.
argument-hint: "[optional: sprint doc path under docs/sprints/SPRINTS]"
---

# /review-sprint — Sprint Review (Verification + Documentation Gates)

## Goal

Run the sprint verification and documentation gates, then update sprint stage/status and resume-fast docs.

This skill is the **only** workflow that should move a sprint to `stage: done`.

## Inputs

$ARGUMENTS

If no sprint path is provided:
- Read `docs/sprints/CURRENT_STATUS.md` and use `active_sprint`.

## Steps

### 0) Load scope

- Read:
  - the sprint doc (argument or `docs/sprints/CURRENT_STATUS.md#active_sprint`)
  - `docs/sprints/CURRENT_STATUS.md`
  - `PROJECT_STATUS.md`

### 1) Verification (required)

Use the sprint doc's **Test plan** section as the source of truth.

- Run every **Automated** command listed there (tests/lint/typecheck/build as specified).
- Execute the **Manual** checklist and record results.

If the sprint doc is missing automated commands, fall back to standard verification:
- lint, typecheck, test, build (as appropriate for your project)

If anything fails:
- Update sprint `stage: verification` and record blockers.
- Stop after documenting what failed and what remains.

### 2) Documentation gates (required when behavior/contracts changed)

Use the sprint doc's **Documentation DoD** as the source of truth.

- If behavior/contracts changed:
  - Load the `documentation-governance` skill
  - Update the correct canonical docs
  - If a contract-impacting change occurred, confirm a plan exists in `docs/plans/` and is approved

### 3) Learnings capture (required)

Review the sprint for reusable knowledge before it's lost.

- **Architectural decisions**: record mid-sprint design choices in the sprint doc Notes section.
- **Patterns discovered**: note new patterns that future work should follow.
- **Pitfalls encountered**: document anything harder than expected or that caused rework.

Keep it brief (3-7 bullets max).

### 4) Skills decision (required)

Decide whether a new skill should be created/updated.

- Check the sprint doc for explicit skill-creation tasks — these are requirements.
- If the work introduced a new reusable procedure or pattern: create or update the relevant `.claude/skills/<skill>/SKILL.md`.
- If neither applies: record "no skill change" in the sprint Notes.

### 5) Plan/idea review (required when sprint originated from a plan or idea)

If the sprint was born from an approved plan or idea doc:
- Compare the outcome against the original vision.
- Note scope changes, deferred items, or new ideas that emerged.
- Update the source plan/idea doc status if appropriate.

### 6) Optional: Solutions compounding (/compound)

If a non-obvious issue was solved during the sprint, run `/compound` to write a solution entry under `docs/solutions/`.

### 7) Close-out updates (required)

- In the sprint doc:
  - ensure acceptance criteria and verification/doc checkboxes are accurate
  - set `stage: done` only when all gates pass
- In `docs/sprints/CURRENT_STATUS.md`:
  - update stage and priorities/blockers
- In `PROJECT_STATUS.md`:
  - update `last_updated` and reflect current active work

## Output

1. Sprint path reviewed
2. Stage before -> after
3. Verification summary (what ran, what passed/failed)
4. Learnings captured (3-7 bullets)
5. Skills outcome (created/updated/no change)
6. Plan/idea alignment (matched / scope changed / deferred items)
7. Docs outcomes (with file links)
