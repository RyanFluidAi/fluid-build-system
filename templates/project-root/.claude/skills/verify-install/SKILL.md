---
name: verify-install
description: Verify FBS installation is complete and correctly configured. Use after installing FBS into a repo to confirm everything is in place.
---

# /verify-install — Verify FBS Installation

## Goal

Confirm every FBS component is present, correctly named, and populated. Report precisely what's missing and how to fix it.

Use Glob and Read. Check for existence **and** non-emptiness — a file that exists but is a stub is a failure, not a pass.

## Steps

### 1) Resume-fast docs (required)

- [ ] `CLAUDE.md` — exists, and `project:` is not still `your-project-name`
- [ ] `PROJECT_STATUS.md` — exists, and `last_updated` is a real date (not `YYYY-MM-DD`)
- [ ] `docs/sprints/CURRENT_STATUS.md` — exists, and `last_updated` is a real date

### 2) Rules (required)

Exactly two rules ship with FBS. Both must be path-scoped via `paths:` frontmatter.

- [ ] `.claude/rules/workflow-sprints.md` — has `paths: ["docs/sprints/**"]`
- [ ] `.claude/rules/workflow-audits.md` — has `paths: ["docs/audits/**"]`

Flag any other file in `.claude/rules/` as an unexpected addition (not an error — just report it, since projects may add their own).

FBS no longer ships `foundation.md` or `workflow-small-fixes.md`; their content lives in `CLAUDE.md`. If either file is present, report it as **stale — delete it, the content is duplicated in CLAUDE.md**.

### 3) Skills (required — all 17)

Every skill must exist at `.claude/skills/<name>/SKILL.md` with frontmatter `name:` **exactly matching the directory name**, lowercase and hyphenated.

Governance pipeline:
- [ ] `new-idea`
- [ ] `review-idea-doc`
- [ ] `new-plan`
- [ ] `check-plan`
- [ ] `new-sprint`
- [ ] `start-sprint`
- [ ] `check-sprint`
- [ ] `review-sprint`

Documentation:
- [ ] `install-documentation`
- [ ] `doc-audit`
- [ ] `doc-sprint-sync`
- [ ] `doc-write`

Audits:
- [ ] `mini-audit`
- [ ] `in-depth-audit`

Utility:
- [ ] `sync`
- [ ] `compound`
- [ ] `verify-install`

Also check:
- [ ] No skill directory exists without a `SKILL.md` inside it
- [ ] These are **absent** — all removed from FBS; report any that are present as stale: `documentation-governance`, `start-session`, `close-session`, `skill-creator`

Report any project-specific skills beyond these 17 separately as additions, not failures. Three to six domain skills is a healthy sign.

### 4) Document scaffolding (required)

- [ ] `docs/ideas/idea-template.md`
- [ ] `docs/plans/plan-template.md`
- [ ] `docs/sprints/sprint-template.md`
- [ ] `docs/solutions/templates/solution-template.md`
- [ ] `docs/audits/templates/mini-audit-template.md`
- [ ] `docs/audits/templates/in-depth-audit-template.md`
- [ ] `docs/audits/active/` and `docs/audits/resolved/` exist
- [ ] `docs/roadmap/ROADMAP.md` exists, or the project has deliberately deleted `docs/roadmap/`

### 5) Canonical documentation (critical)

Verify `docs/reference/` exists and each doc is **populated**, not a placeholder template.

- [ ] `PLATFORM_OVERVIEW_CANONICAL.md`
- [ ] `SCHEMA_AND_CONTRACTS_CANONICAL.md`
- [ ] `GLOBAL_TERMINOLOGY_INDEX_CANONICAL.md`
- [ ] `DOCUMENTATION_INVENTORY.md`

**Placeholder detection** — a doc is unpopulated if it contains any of:
- `YYYY-MM-DD` in frontmatter dates
- Bracket placeholders like `[definition]`, `[What this platform is]`, `[primary user types]`
- Ellipsis `…` as the sole content of a section
- Template markers like `[present/missing]`, `[term]`, `[Name]`

Also flag as **stale — safe to delete** any of these, which FBS no longer ships (their content moved into the `doc-write` skill): `DOCUMENTATION_STANDARDS_CANONICAL.md`, `DOCUMENTATION_HIERARCHY_CANONICAL.md`, `DOCUMENT_MAINTENANCE_GUIDE_CANONICAL.md`, `DOCUMENT_TEMPLATE_CANONICAL.md`.

### 6) Numbering consistency

For each `docs/ideas/IDEA-*.md`, `docs/plans/PLAN-*.md`, and `docs/sprints/SPRINT-*.md`:

- [ ] The `number:` in frontmatter matches the `NNN` in the filename
- [ ] No two documents of the same type share a number
- [ ] Numbers are zero-padded to 3 digits

Report gaps in the sequence as informational only — deleted documents leave holes, which is fine.

### 7) Plan discipline spot-check

For each plan in `docs/plans/`:
- [ ] No "Alternatives Considered" section (these belong in idea docs)
- [ ] At most one sprint referenced in the Implementation Record
- [ ] Every plan with `status: implemented` has its Implementation Record filled

Report violations as warnings with the plan number.

### 7b) Link integrity

- [ ] Every sprint's `plan:` frontmatter points at a plan that exists
- [ ] Every such plan's Implementation Record names that same sprint (bidirectional link intact)
- [ ] No two sprints claim the same plan
- [ ] If `docs/roadmap/ROADMAP.md` exists, every sprint's `roadmap_phase:` names a phase that exists in it

### 8) Things that should NOT be present

FBS deliberately ships no hooks, no agent definitions, and no settings file. Report each of these as **stale — safe to delete**, not as an error, since a project may have added its own on purpose:

- `.claude/agents/`
- `.claude/hooks/`
- `.claude/settings.json` containing FBS's old hook block
- `.mcp.json` containing an empty `{"mcpServers": {}}`
- `docs/sessions/` — session logging was removed from FBS

If the project has added its own agents or hooks, say so and move on — that's the project's call, not an FBS requirement.

## Report

Produce a structured summary:

- **Required**: pass/fail per item, grouped by section
- **Canonical docs**: OK / UNPOPULATED / MISSING per file
- **Numbering**: consistent / conflicts found (list them)
- **Stale artifacts**: anything from section 8, with a one-line "safe to delete"
- **Project additions**: skills and rules beyond the FBS set (informational)
- **Verdict**: Complete | Incomplete (N required items missing)
- **Next steps**: concrete commands. Always recommend `/install-documentation` if any canonical doc is unpopulated or missing.
