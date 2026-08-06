# Roadmap

`ROADMAP.md` is the only document that answers "why are we building this now?" Sprints say what and how; the roadmap says which goal the sprint advances.

## How it stays current

The roadmap is maintained by the sprint lifecycle, not by hand:

| When | What happens | Who does it |
|------|--------------|-------------|
| Plan created | The plan is added to its phase's **Related plans** list | `/new-plan` |
| Sprint created | `roadmap_phase:` is set in the sprint's frontmatter, and the sprint is added to that phase's **Related sprints** list | `/new-sprint` |
| Sprint closed | The phase's deliverable checkboxes are ticked; if all are done, phase status moves to `completed` and the next phase becomes `in progress` | `/review-sprint` |

`PROJECT_STATUS.md` carries `roadmap_phase:` so a new session knows the current phase without opening this file.

A sprint may legitimately have no phase — bug fixes and maintenance sprints usually don't. Set `roadmap_phase: ""` and move on. Nothing blocks.

## Structure

- Phases are sequential or parallel tracks, each with status `not started` / `in progress` / `completed`.
- Deliverables are checkboxes. They are the unit `/review-sprint` ticks, so write them as observable outcomes, not activities.
- Keep phases high-level. Task breakdowns belong in sprint docs.

## If you don't use a roadmap

Delete this directory. Nothing else in FBS depends on it — the sprint skills skip their roadmap steps when `ROADMAP.md` is absent.
