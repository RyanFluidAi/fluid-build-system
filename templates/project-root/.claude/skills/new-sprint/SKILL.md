---
name: new-sprint
description: Create a sprint document from an approved plan or direction. Use when ready to break down approved work into an agent-executable sprint plan.
argument-hint: "[sprint name or topic] [optional: dates] [optional: goal]"
---

# /new-sprint — Create a Sprint Document

## Goal

Create a sprint document that breaks down approved work into actionable tasks for implementation.

## Inputs

$ARGUMENTS

If minimal input is provided, ask the user for the sprint name/topic, planned dates, and goal.

## Steps

### 1) Gather context

- If coming from an approved plan in `docs/plans/`, read it for scope and acceptance criteria.
- Read `docs/sprints/sprint-template.md` for the required structure.
- Optionally review recent sprints in `docs/sprints/SPRINTS/` for examples.

### 2) Create the sprint file

- **Filename**: `docs/sprints/SPRINTS/YYYY-MM-DD-<sprint-name>.md`
- **Frontmatter**:
  - `doc_type: sprint`
  - `status: active`
  - `stage: planning`
  - `created: YYYY-MM-DD`
  - `last_updated: YYYY-MM-DD`
  - `dates: { start: YYYY-MM-DD, end: YYYY-MM-DD }`
  - `sprint_goal: <single clear sentence>`

### 3) Fill each section

- **Summary**: one-liner of what the sprint delivers
- **Acceptance criteria**: measurable/verifiable checkboxes
- **Contracts / governance**: references to approved plans (if contract changes involved)
- **Work plan**: task breakdown with To do / In progress / Done
- **Test plan**: Automated commands + Manual checklist items
- **Review gate**: what must pass before sprint can close
- **Documentation DoD**: specific canonical docs to create or update
- **Audit plan**: mini audit, in-depth, or "none needed" with rationale
- **Notes**: empty section for runtime decisions

### 4) Update status docs

- Update `docs/sprints/CURRENT_STATUS.md` to set the active sprint.
- Update `PROJECT_STATUS.md` if priorities changed.

## Output

1. Sprint file path created
2. Stage: planning
3. Summary of acceptance criteria
4. Next step: use `/start-sprint` to begin implementation
