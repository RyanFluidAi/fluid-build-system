---
name: new-sprint
description: Create a sprint document that turns approved work into an agent-executable plan with parallel workstreams. Use when ready to break down work into tasks that sub-agents can execute concurrently.
argument-hint: "[topic] [optional: plan number] [optional: dates]"
---

# /new-sprint — Create a sprint document

## Goal

Turn a decided direction into an **agent-executable work plan**: crisp acceptance criteria, a task list that maps to real files, and independent workstreams that parallel sub-agents can run concurrently.

## When to use

- **Tier 3** (contract changes): the sprint executes exactly one approved plan from `docs/plans/`. Link it. If the plan isn't approved yet, still create the sprint but leave it in `stage: planning` and record the approval dependency.
- **Tier 2** (no contract changes): no plan needed. Create the sprint directly once acceptance criteria are clear.

**One plan produces one sprint.** If you are about to create a second sprint from a plan that already has one, stop — the plan was too big and should be split into multiple plans instead.

## Inputs

$ARGUMENTS

Gather, or infer from `docs/sprints/CURRENT_STATUS.md` and the source plan:

- **Topic** — 3-8 words, kebab-case friendly
- **Dates** — start/end (YYYY-MM-DD)
- **Goal** — one sentence
- **Scope** — in-scope areas and explicit non-goals
- **Source plan** — required for Tier 3

If input is thin, ask for the topic, dates, and goal. Don't ask for anything you can read off the plan.

## Steps

### 1) Load context

Read:
- `docs/sprints/sprint-template.md` — required structure
- `docs/sprints/CURRENT_STATUS.md` — active sprint, stage, priorities
- the source plan in `docs/plans/`, if Tier 3

Note any other sprints still open. This never blocks sprint creation — see step 6.

### 2) Back-check: does this plan already have a sprint? (Tier 3 only)

Before creating anything, verify the one-plan-one-sprint rule from both directions:

- Grep `docs/sprints/` for any sprint whose frontmatter `plan:` points at this plan.
- Read the source plan's **Implementation Record → Sprint** field.

**If either shows an existing sprint**, stop and report:

> `PLAN-NNN` already has a sprint: `SPRINT-MMM`. A plan produces exactly one sprint.

Then give the user the two real options and wait for an answer:

1. **The plan was too big** — split it into a follow-on plan covering the remaining work, and create that plan's sprint instead. This is almost always the right move.
2. **The existing sprint is abandoned** — mark it `status: abandoned`, clear the plan's Sprint field, and proceed.

Do not create a second sprint against the same plan without an explicit instruction. This is a conflict check, not a workflow gate — it fires only when something has genuinely gone wrong.

Also verify the plan's `status:` is `approved`. If it is still `draft`, create the sprint but keep it in `stage: planning` and record the approval dependency as a blocker.

### 3) Assign the number and create the file

**Filename**: `docs/sprints/SPRINT-NNN-YYYY-MM-DD-<topic>.md`

- Scan `docs/sprints/` for existing `SPRINT-NNN-*.md` and take the next sequential number, zero-padded to 3 digits. First sprint is `SPRINT-001`.
- `YYYY-MM-DD` is today (creation date), not the start date.
- Set `number: NNN` in frontmatter to match the filename.

Frontmatter: `doc_type: sprint`, `number`, `status: active`, `stage: planning`, `created`, `last_updated`, `dates.start`, `dates.end`, `sprint_goal`, `plan:` (the source plan, or empty for Tier 2), and `roadmap_phase:`.

For `roadmap_phase:`: if `docs/roadmap/ROADMAP.md` exists, read it and set the phase this sprint advances. Bug-fix and maintenance sprints often advance none — set `""` and move on. Skip this entirely if there is no roadmap.

### 4) Write the work plan

Fill the template with no placeholders left behind.

**Acceptance criteria** — 5-12 checkboxes, written as observable outcomes. "The `/orders` endpoint returns `settled_at` on every order" beats "settlement works."

**Tasks** — each maps to a real repo location or a concrete deliverable, and carries three tags:
- domain: `[DB]` `[API]` `[UI]` `[Test]` `[Config]` `[Docs]`
- priority: `[P0]` `[P1]` `[P2]`
- rough estimate

**Parallel workstreams** — this is the part that makes the sprint fast. Group tasks into named tracks that touch **disjoint sets of files**, so each track can be handed to its own sub-agent running concurrently.

For each track, record:
- track name and the domain(s) it covers
- the files or directories it owns — no two concurrent tracks may list the same file
- what it depends on (another track, or nothing)
- how a sub-agent knows it's done

Then state the execution order: which tracks start together, and which wait on a dependency. A typical shape is a sequential spine with parallel branches — schema lands first, then API and UI tracks run concurrently against it, then integration tests join them.

If the work genuinely can't be split — a single tightly-coupled change — say so explicitly in the section and give one track. Don't invent artificial parallelism; overlapping file ownership causes more rework than it saves.

**Test plan** — automated commands to run and what each covers, plus a manual checklist for the primary flows.

**Review gate** — what must be true before `/review-sprint` can close it.

Keep the sprint doc proportional to the work. A four-task sprint doesn't need a six-track workstream diagram.

### 5) Update status docs and close the link

- `docs/sprints/CURRENT_STATUS.md`: set `active_sprint` to the new path, `stage: planning`, adjust priorities, and add a blocker if waiting on plan approval.
- `PROJECT_STATUS.md`: update `last_updated` and `active_work`.
- **`docs/roadmap/ROADMAP.md`** (if it exists and a phase is set): add this sprint to that phase's **Related sprints** list. If the phase status is `not started`, move it to `in progress`.
- **`PROJECT_STATUS.md`**: set `roadmap_phase:` to the sprint's phase.
- **The source plan** (Tier 3): write `SPRINT-NNN` into its **Implementation Record → Sprint** field. This closes the bidirectional link — the sprint's `plan:` frontmatter points up, the plan's Sprint field points down — and is what makes the back-check in step 2 work for the next person.

### 6) Present the result

Report, in this order:

1. **`SPRINT-NNN` created at `docs/sprints/SPRINT-NNN-YYYY-MM-DD-<topic>.md`** — lead with the assigned number and full path.
2. Sprint goal and dates.
3. Source plan, if any, and whether its approval is still pending.
4. The parallel workstreams: track names, what each owns, and how many can run concurrently.
5. **Other open sprints**, if any — name them and their stages, as a one-line warning. Ideally they'd be in `verification` or `done` first, but this does not block anything.
6. Next step: `/start-sprint`.

## Notes

- This skill creates the work plan; it does not implement it.
- Keep `stage` consistent between frontmatter, the Summary section, and `CURRENT_STATUS.md`.
- For Tier 3, don't begin implementation until plan approval exists.
- Canonical documentation is not a sprint-close gate. Include doc tasks only if the sprint's own deliverable is documentation.
