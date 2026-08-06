---
name: start-sprint
description: Start implementing a sprint work plan (execution only, no verification gates). Use when ready to begin coding against a sprint doc. Runs the sprint's parallel workstreams as concurrent sub-agents.
argument-hint: "[optional: sprint doc path under docs/sprints/]"
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

### 1) Check other open sprints (warn, never block)

Scan `docs/sprints/` for other sprints with `status: active` and a stage of `planning` or `in_progress`.

If any exist, emit a **one-line warning** naming them and their stages — ideally earlier sprints reach `verification` or `done` before the next starts. Then **continue**. This is advisory only. Do not stop, do not ask permission, do not refuse to start work on this basis.

### 2) Confirm stage transition (planning -> in_progress)

- If the sprint is `stage: planning`, set it to `stage: in_progress`.
- Update `docs/sprints/CURRENT_STATUS.md#stage` accordingly.
- For Tier 3 sprints, confirm the linked plan is approved before writing code. This is the one gate that does block.

### 3) Execute the work plan

Read the sprint's **Parallel Workstreams** table and execute accordingly.

- Convert the **Work Plan** tasks into an explicit task list.
- Respect the declared execution order: run tracks with no unmet dependencies concurrently, then the tracks that depended on them.
- **Delegate each concurrent track to its own general-purpose sub-agent**, launched in a single message so they run in parallel. Give each sub-agent: its track's tasks, the files it owns, the acceptance criteria it serves, and an instruction to stay inside its file set.
- Never run two concurrent tracks that claim the same file. If tracks turn out to overlap in practice, collapse them and run sequentially.
- Run a single track inline rather than delegating — sub-agents are for concurrency, not ceremony.
- Update the sprint doc continuously:
  - move tasks from To Do → In Progress → Done
  - record key decisions and any track collisions in Notes

### 4) Testing while implementing (allowed, not the gate)

- Run targeted checks as needed while implementing to avoid accumulating failures.
- Standard verification commands (adapt to your project):
  - lint
  - typecheck
  - test
  - build (only if relevant to the sprint)
- Scoped checks are fine when only one area is being changed.
- Do not treat this as "verification complete".

### 5) Handoff to verification

When implementation is functionally complete:
- Update sprint stage to `verification`.
- Optionally run `/check-sprint` for a deep code review, and fix any P0/P1 issues it finds. Worth doing on contract changes or large diffs; skip it on small ones.
- Run `/review-sprint` to execute the verification gates and close the sprint.

## Output

1. Sprint number and path being executed
2. Stage before -> after
3. Other open sprints, if any (advisory warning only)
4. Tracks run, and which ran concurrently
5. What was completed in this run
6. What remains
