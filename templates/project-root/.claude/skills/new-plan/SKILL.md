---
name: new-plan
description: Create a formal plan document for contract-impacting changes (schema/API/integration/business rules). Use when a change requires explicit approval before implementation.
argument-hint: "[topic]"
---

# /new-plan — Create a plan document

## Goal

Write the authoritative specification for **one** contract-impacting change, in enough detail that a single sprint can execute it without further design decisions.

## When to use

Create a plan for changes to:
- **Database schemas** — tables, columns, constraints, migrations
- **API contracts** — endpoints, request/response shapes, error envelopes
- **Integration contracts** — external payloads, webhooks, partner APIs
- **Business rules** — documented canonical behavior

Not required for internal refactors with no contract change, or bug fixes that restore compliance with an existing spec. Those are Tier 1 or Tier 2 work — see `CLAUDE.md`.

## The three rules of a plan

These are what separate a plan from an idea doc. Violating them is the most common failure mode.

### 1. One direction, stated as fact

A plan records a decision that has already been made. Write it in the indicative: "The `orders` table gains a `settled_at` timestamp." Not "we could add a timestamp" or "one option is to add a timestamp."

**Do not include an "Alternatives Considered" section.** Alternatives are explored in `/new-idea` and live in the idea doc. By the time a plan is written the choice is made, and the losing options are noise to the person implementing it. If a genuine fork in the road is still open, you are not ready to write a plan — go back to `/new-idea`.

**Banned in a plan**: "maybe", "possibly", "we could", "one approach", "alternatively", "TBD", "either X or Y", "depending on", "if we decide", "some kind of", "or similar". Scan for these before presenting.

The only permitted uncertainty is a **Risk** with a named mitigation, or an explicit **Open Decision** that the approver must resolve — and an Open Decision blocks approval. If there is more than one, the plan isn't ready.

### 2. One plan, one sprint

A plan produces exactly one sprint. Do not write phased plans with "Phase 1 / Phase 2 / Phase 3" that imply multiple sprints, and do not write a plan whose implementation impact obviously exceeds what one sprint can deliver.

If the work is too large, split it into several plans, each independently approvable and each producing one sprint. Sequence them by number and record the dependency in Traceability. A plan that says "this will take three sprints" is three plans.

Phases *within* the single sprint (DB → API → UI) are fine — that's execution ordering, not scope splitting.

### 3. Specific enough to build from

Every file path must be a real path in this repo. Every schema and signature must follow the patterns already used here. If you find yourself writing a generic example, go read the actual code first.

## Steps

### 1) Gather context

If coming from `/new-idea`, you have the problem statement, chosen architecture, and key decisions. Use them.

If not, establish what contract is changing, why, and what the decided direction is. If the user is still weighing options, stop and run `/new-idea` instead.

### 2) Read the template and the code

- Read `docs/plans/plan-template.md` for the required structure.
- Read the actual schema files, route definitions, and type definitions the change touches. The specification section must reflect real code.
- Review a recent plan in `docs/plans/` for house style.

### 3) Assign the number and create the file

**Filename**: `docs/plans/PLAN-NNN-YYYY-MM-DD-<short-topic>.md`

- Scan `docs/plans/` for existing `PLAN-NNN-*.md` and take the next sequential number, zero-padded to 3 digits. First plan is `PLAN-001`.
- `YYYY-MM-DD` is today. `<short-topic>` is kebab-case, 3-6 words.
- Set `number: NNN` in frontmatter to match the filename.

### 4) Write the plan

Fill every section of the template with real content:

- **Problem Statement** — current state with file references, desired state as concrete outcomes, why it matters.
- **Decision** — one paragraph stating the direction in the indicative. This is the heart of the plan.
- **Specification** — schema definitions, endpoint signatures, worked examples, semantics. Use the schema + example + semantics pattern.
- **Canonical Changes Required** — which docs in `docs/reference/` change, and whether each is Patch / Minor / Major.
- **Implementation Impact** — files to modify, new files, tests to add or update, migration approach.
- **Migration Plan** — required if breaking: numbered steps and a rollback procedure. Otherwise state "No breaking changes."
- **Risks** — table with likelihood, impact, and a concrete mitigation. "We'll be careful" is not a mitigation. Do not claim there are no risks.
- **Traceability** — idea doc, canonical docs, anticipated implementation and test files, related audits, and any plan this one depends on.

### 5) Link the roadmap

If `docs/roadmap/ROADMAP.md` exists, add this plan to the **Related plans** list of the phase it advances, and record that phase in the plan's Traceability section. Skip if there is no roadmap, or if the plan advances no phase.

### 6) Self-check before presenting

- [ ] Filename and `number:` match, format is `PLAN-NNN-YYYY-MM-DD-<topic>.md`
- [ ] No "Alternatives Considered" section exists
- [ ] Zero banned hedging terms outside the Risks table
- [ ] Scope fits one sprint
- [ ] Every file path referenced exists in the repo
- [ ] Schema and API examples follow this project's actual patterns
- [ ] Migration impact assessed; rollback described if breaking
- [ ] Risks have concrete mitigations
- [ ] No section left as a placeholder

### 7) Present and stop

Report, in this order:

1. **`PLAN-NNN` created at `docs/plans/PLAN-NNN-YYYY-MM-DD-<topic>.md`** — lead with the assigned number and full path.
2. The decision, in one or two sentences.
3. Contracts affected and the change type for each.
4. Top risks and their mitigations.
5. Any Open Decisions blocking approval.
6. That the next step is approval, then `/new-sprint` — producing one sprint.

**Do not implement anything until the user explicitly approves.**

The user may approve as-is, request changes (update and re-present), or reject (return to `/new-idea`).

## After approval

1. User sets `status: approved` and fills the approval date.
2. Run `/new-sprint` — it creates the single sprint that executes this plan.
3. Implement with traceability: reference `PLAN-NNN` in commits.
4. Mark the plan `status: implemented` when the sprint closes, and record the sprint number in the Implementation Record.

## Common failure modes

- Hedged language that leaves the implementer guessing — the top cause of bad sprints.
- Carrying alternatives forward from the idea doc into the plan.
- A plan sized for three sprints, then executed as one rushed sprint.
- Generic examples instead of the project's real patterns.
- Missing migration plan on a schema change.
- Vague problem statements ("we need to improve X").
- "No risks."
