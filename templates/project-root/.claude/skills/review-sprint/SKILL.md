---
name: review-sprint
description: Verify a sprint and close it (tests + docs + audit/skills gates). The only workflow that should move a sprint to stage done.
argument-hint: "[optional: sprint doc path under docs/sprints/]"
---

# /review-sprint — Sprint Review (Verification + Documentation Gates)

## Goal

Run the sprint verification and documentation gates, then update sprint stage/status and resume-fast docs.

This skill is the **only** workflow that should move a sprint to `stage: done`.

## Inputs

$ARGUMENTS

If no sprint path is provided:
- Read `docs/sprints/CURRENT_STATUS.md` and use `active_sprint`.

Optional flag:
- `--deep` enables heavyweight review using parallel reviewer subagents.

## What this skill owns (no crossover)

- **Verification**: execute the sprint's test plan (automated + manual) and record results.
- **Documentation**: analyze what was built and update canonical docs under `docs/reference/` to reflect the current state of the codebase.
- **Audit + skills decision**: ensure audit state is correct and decide whether a new skill is required.

## Steps

### 0) Load scope

- Read:
  - the sprint doc (argument or `docs/sprints/CURRENT_STATUS.md#active_sprint`)
  - `docs/sprints/CURRENT_STATUS.md`
  - `PROJECT_STATUS.md`
  - `docs/audits/active/` (only if sprint touched canonical surfaces)

If `--deep` is enabled:
- Run parallel reviewer subagents and synthesize findings before proceeding:
  - `architecture-reviewer`
  - `security-reviewer`
  - `performance-reviewer`
  - `data-integrity-reviewer`
  - `test-quality-reviewer`
  - `docs-governance-reviewer`

### 1) Verification (required)

Use the sprint doc's **Test plan** section as the source of truth.

- Run every **Automated** command listed there (tests/lint/typecheck/build as specified).
- Execute the **Manual** checklist and record results.

If the sprint doc is missing automated commands, fall back to standard verification:
- lint, typecheck, test, build (as appropriate for your project)

If anything fails:
- Update sprint `stage: verification` and record blockers in the sprint doc and `docs/sprints/CURRENT_STATUS.md`.
- Stop after documenting what failed and what remains.

### 2) Documentation gates (required)

Document the code that was built by updating canonical docs under `docs/reference/`. This step always runs — it is not conditional.

#### 2a) Determine what changed

- Read the sprint doc's **Work Plan** (Done section) and **Acceptance Criteria** to understand what was built.
- Use `git diff` against the sprint's starting point (or the base branch) to identify changed files.
- Categorize changes: new endpoints, new/modified schemas, new entities, new integrations, UI changes, config changes.

#### 2b) Launch documentation sub-agents in parallel

Spawn sub-agents using the Agent tool to update the canonical docs based on what changed. Only launch agents for docs that need updating — skip any that are unaffected.

**Sub-agent: Platform Overview updater** (launch if architecture, system boundaries, or core concepts changed)
```
Prompt: "Read docs/reference/PLATFORM_OVERVIEW_CANONICAL.md and the following changed files: [list].
Update the canonical doc to reflect any changes to: system boundaries, architecture components, core domain concepts, invariants, users/roles, or integrations.
Follow the documentation-governance rules: declarative tone, no silent invention, preserve existing wording unless explicitly changed.
Set last_updated to today's date."
```
- **subagent_type**: `general-purpose`, **mode**: `acceptEdits`

**Sub-agent: Schema & Contracts updater** (launch if DB schemas, API routes, types, or validation changed)
```
Prompt: "Read docs/reference/SCHEMA_AND_CONTRACTS_CANONICAL.md and the following changed files: [list].
Update the canonical doc to reflect: new/modified entities, changed fields or constraints, new/modified API endpoints (with request/response shapes), updated error models, or versioning changes.
Use schema + example + semantics format for each new operational structure.
Follow the documentation-governance rules: declarative tone, no silent invention, preserve existing wording unless explicitly changed.
Set last_updated to today's date."
```
- **subagent_type**: `general-purpose`, **mode**: `acceptEdits`

**Sub-agent: Terminology updater** (launch if new domain terms, entity names, or status values were introduced)
```
Prompt: "Read docs/reference/GLOBAL_TERMINOLOGY_INDEX_CANONICAL.md and the following changed files: [list].
Add definitions for any new domain-specific terms, entity names, status values, or abbreviations introduced in this sprint.
Do not duplicate existing terms. Use declarative definitions specific to this project.
Set last_updated to today's date."
```
- **subagent_type**: `general-purpose`, **mode**: `acceptEdits`

#### 2c) Contract-impacting changes gate

- If the sprint changed existing canonical contracts (schemas/API/DB/business rules):
  - Confirm a plan exists in `docs/plans/` and is approved
  - Do not update canonical docs for contract changes unless the plan is approved
- If the sprint only added new behavior without changing existing contracts, canonical docs can be updated without a plan.

#### 2d) Update the Documentation Inventory

After sub-agents complete, update `docs/reference/DOCUMENTATION_INVENTORY.md` to reflect any new sections or gaps identified.

### 3) Audit system sync (only when canonical surfaces are touched)

- Ensure a mini audit exists/updated under `docs/audits/active/` when the sprint touched canonical surfaces.
- Ensure the audit includes:
  - canonical refs reviewed
  - issues (if any)
  - testing performed

### 4) Learnings capture (required)

Review the sprint for reusable knowledge before it's lost.

- **Architectural decisions**: record mid-sprint design choices (e.g., "chose X over Y because Z") in the sprint doc Notes section.
- **Patterns discovered**: note new patterns that future work should follow. Examples: a standard integration flow, a data-channel relay pattern, a configuration-over-code approach.
- **Pitfalls encountered**: document anything harder than expected or that caused rework, even if it didn't require a `/compound` entry.

Keep it brief (3-7 bullets max). The goal is to make the *next* sprint in this area faster.

### 5) Skills decision (required)

Decide whether a new skill should be created/updated.

- **Check the sprint doc first**: look for any explicit skill-creation tasks in the sprint. These are requirements, not suggestions.
- If the sprint doc requests a skill: create it.
- If the work introduced a new reusable procedure, repeated pitfall, or standardized pattern worth codifying (even if the sprint doc didn't ask): create or update the relevant `.claude/skills/<skill>/SKILL.md`.
- If neither applies: explicitly record "no skill change" in the sprint Notes.

### 6) Plan/idea review (required when sprint originated from a plan or idea)

If the sprint was born from an approved plan (`docs/plans/`) or idea doc (`docs/ideas/`):

- Compare the outcome against the original vision. Did we build what we planned?
- Note any scope changes, deferred items, or new ideas that emerged.
- Update the source plan/idea doc status if appropriate (e.g., mark phases as complete, add "emerged during Sprint X" notes).

### 7) Optional: Solutions compounding (/compound)

If a non-obvious issue was solved (debugging required, likely to recur), run `/compound` after review to write a solution entry under `docs/solutions/`.

### 8) Close-out updates (required)

- In the sprint doc:
  - ensure acceptance criteria and verification/doc/audit checkboxes are accurate
  - set `stage: done` only when all gates pass
- In `docs/sprints/CURRENT_STATUS.md`:
  - update stage and priorities/blockers
- In `PROJECT_STATUS.md`:
  - update `last_updated` and reflect current active work if it changed

## Output

At the end, provide:

1. Sprint path reviewed
2. Stage before -> after
3. Verification summary (what ran, what passed/failed)
4. Learnings captured (key decisions, patterns, pitfalls - 3-7 bullets)
5. Skills outcome (created/updated/no change - with file links if applicable)
6. Plan/idea alignment (matched / scope changed / deferred items noted)
7. Docs/audit outcomes (with file links and canonical docs updated)
