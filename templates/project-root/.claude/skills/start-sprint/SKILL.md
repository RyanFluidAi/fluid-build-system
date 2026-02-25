---
name: start-sprint
description: Start implementing a sprint work plan (execution only, no verification gates). Use when ready to begin coding against a sprint doc.
argument-hint: "[optional: sprint doc path under docs/sprints/SPRINTS]"
---

# /start-sprint — Execute a Sprint (Implementation Only)

## Goal

Start implementing the sprint work plan and keep the sprint doc up to date.

This skill does not run the sprint verification gates and does not move a sprint to `stage: done`. Use `/review-sprint` for verification + documentation + close-out.

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

### 1) Confirm stage transition (planning -> in_progress)

- If sprint is `stage: planning`, set it to `stage: in_progress`.
- Update `docs/sprints/CURRENT_STATUS.md#stage` accordingly.

### 2) Execute the work plan

- Convert sprint **Work plan** tasks into an explicit task list.
- Implement tasks in a logical order (DB -> API -> UI -> tests scaffolding -> docs scaffolding).
- Update the sprint doc continuously:
  - move tasks from To do -> In progress -> Done
  - record key decisions in Notes

### 3) Testing while implementing (allowed, not the gate)

- Run targeted checks as needed while implementing to avoid accumulating failures.
- Do not treat this as "verification complete".

### 4) Handoff to /review-sprint

When implementation is functionally complete:
- Update sprint stage to `verification`.
- Run `/review-sprint` to execute the verification + documentation gates and close the sprint.

## Output

1. Sprint path being executed
2. Stage before -> after
3. What was completed in this run
4. What remains
