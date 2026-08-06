---
name: review-sprint
description: Verify a sprint and close it. Runs the sprint's test plan, captures learnings, and updates status docs. The only workflow that should move a sprint to stage done.
argument-hint: "[optional: sprint doc path under docs/sprints/]"
---

# /review-sprint — Sprint Review (Verification Gates)

## Goal

Run the sprint verification gates, then update sprint stage/status and resume-fast docs.

This skill is the **only** workflow that should move a sprint to `stage: done`.

Documentation sync is **not** part of this workflow. Canonical docs under `docs/reference/` are updated manually via `/doc-sprint-sync` when the owner decides it's worth doing.

## Inputs

$ARGUMENTS

If no sprint path is provided:
- Read `docs/sprints/CURRENT_STATUS.md` and use `active_sprint`.

Deep code review is not part of this skill. Run `/check-sprint` before this if the diff warrants it.

## What this skill owns (no crossover)

- **Verification**: execute the sprint's test plan (automated + manual) and record results.
- **Audit + skills decision**: ensure audit state is correct and decide whether a new skill is required.

Documentation sync is **not** owned by this skill. Run `/doc-sprint-sync` manually when you want canonical docs refreshed.

## Steps

### 0) Load scope

- Read:
  - the sprint doc (argument or `docs/sprints/CURRENT_STATUS.md#active_sprint`)
  - `docs/sprints/CURRENT_STATUS.md`
  - `PROJECT_STATUS.md`
  - `docs/audits/active/` (only if sprint touched canonical surfaces)

### 1) Verification (required)

Use the sprint doc's **Test plan** section as the source of truth.

- Run every **Automated** command listed there (tests/lint/typecheck/build as specified).
- Execute the **Manual** checklist and record results.

If the sprint doc is missing automated commands, fall back to standard verification:
- lint, typecheck, test, build (as appropriate for your project)

If anything fails:
- Update sprint `stage: verification` and record blockers in the sprint doc and `docs/sprints/CURRENT_STATUS.md`.
- Stop after documenting what failed and what remains.

### 2) Documentation (manual, not gated)

Canonical documentation sync is **not** part of this workflow. Do not spawn doc sub-agents here and do not block sprint close on documentation.

- If the sprint changed contracts/behavior that require canonical doc updates, surface this in the output as a reminder (e.g., "Owner may want to run `/doc-sprint-sync` — schema/API changed").
- If a contract-impacting change occurred and the plan approval gate is still pending, note that as a blocker — but doc sync itself is still the owner's call, run manually.

### 3) Audit reminder (not gating)

If the sprint touched canonical surfaces (schema, API, DB, integrations), surface a one-line reminder that `/mini-audit` is worth running. Do not block sprint close on it, and do not write the audit from here.

### 4) Capture what's worth keeping (only if there is something)

Skip this section outright when the sprint produced nothing reusable. Most sprints do. Recording "no learnings" is not a deliverable — say nothing.

Write into the sprint doc Notes only when one of these actually happened:

- **A decision worth remembering** — a mid-sprint design choice a future reader would otherwise have to reverse-engineer.
- **A pattern worth repeating** — something the next sprint in this area should follow.
- **A pitfall worth warning about** — something that caused real rework.

3-7 bullets, hard cap. If a solved problem is non-obvious and likely to recur, that's a `/compound` entry instead, not a Notes bullet.

### 5) Skills decision (only if the sprint asked, or the work demands it)

- **If the sprint doc contains an explicit skill-creation task**: that's a requirement. Create it.
- **If the work established a repeatable procedure** someone will otherwise re-derive: create or update the relevant `.claude/skills/<skill>/SKILL.md`.
- **Otherwise**: say nothing. Do not record "no skill change" anywhere.

### 6) Plan reconciliation (Tier 3 only)

If the sprint executed a plan:

- Compare what shipped against the plan's Decision and Specification. Flag scope gaps, scope creep, and deviations.
- Set the plan's `status: implemented` and fill its **Implementation Record**: sprint number, date, and any deviations. An unfilled Implementation Record breaks the back-check `/new-sprint` relies on.

### 7) Roadmap advancement (only if a roadmap exists)

If `docs/roadmap/ROADMAP.md` exists and the sprint has a `roadmap_phase:`:

- Tick the phase deliverables this sprint completed.
- If every deliverable in the phase is now ticked, set the phase to `completed` and move the next phase to `in progress`.
- Update `roadmap_phase:` in `PROJECT_STATUS.md`.

Skip silently if there is no roadmap or no phase set.

### 8) Close-out (required)

- In the sprint doc: verify acceptance criteria and checkboxes are accurate, then set `stage: done` — only when verification passed.
- In `docs/sprints/CURRENT_STATUS.md`: update stage, priorities, and blockers.
- In `PROJECT_STATUS.md`: update `last_updated`, `active_work`, `active_sprint`, and the P0/P1 counts if they changed.

## Output

At the end, provide:

1. Sprint number and path reviewed
2. Stage before -> after
3. Verification summary — what ran, what passed, what failed
4. Plan reconciliation (Tier 3) — matched / scope changed / deviations, and confirmation the Implementation Record is filled
5. Roadmap — phase advanced, or omitted

Include only if they apply — omit the line entirely otherwise, don't write "N/A":

6. Learnings captured
7. Skill created or updated
8. Reminders — `/mini-audit` if canonical surfaces were touched, `/doc-sprint-sync` if contracts changed, `/compound` if a non-obvious problem was solved

---

## Position in the workflow

`/review-sprint` is the **final step** in the sprint lifecycle. Run it once implementation is complete — and, if `/check-sprint` was run, once its P0/P1 issues have been resolved.

The full sprint workflow is:

```
/new-sprint    → create sprint execution plan with parallel workstreams
/start-sprint  → execute implementation (concurrent sub-agents per track)
/check-sprint  → deep code review of completed work (optional)
   fix issues found
/review-sprint → verification gates + close sprint (this skill)
```

After a sprint is closed, consider:
- **`/doc-sprint-sync`** — if the sprint changed contracts or behavior worth documenting in `docs/reference/`
- **`/mini-audit`** — if canonical surfaces were touched
- **`/compound`** — if a non-obvious problem was solved, capture it as a solution entry
