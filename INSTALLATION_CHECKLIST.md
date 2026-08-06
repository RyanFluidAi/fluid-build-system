---
doc_type: fbs_installation_checklist
status: active
created: 2026-01-25
updated: 2026-08-06
---

# FBS (Fluid Build System) — Installation Checklist (1 page)

Use this checklist when installing into a **new** or **existing** repo.

## 0) Confirm upstream Claude Code conventions (do first)

- [ ] Review: [CLAUDE.md and Memory](https://code.claude.com/docs/en/memory)
- [ ] Review: [Skills](https://code.claude.com/docs/en/skills)
- [ ] Review: [Subagents](https://code.claude.com/docs/en/sub-agents)
- [ ] Review: [Settings](https://code.claude.com/docs/en/settings)

## 1) Copy the starter tree

- [ ] Copy everything under `templates/project-root/` into the destination repo root.

FBS ships no hooks, no agent definitions, and no `settings.json`. If the destination repo already has `.claude/settings.json`, `.claude/hooks/`, or `.claude/agents/`, leave them alone — nothing in FBS depends on them and nothing will overwrite them.

## 2) Required "resume fast" docs (fill immediately)

- [ ] `CLAUDE.md` — set `project:`, tech stack, and conventions
- [ ] `PROJECT_STATUS.md` — set `last_updated` and current state
- [ ] `docs/sprints/CURRENT_STATUS.md` — set active work, or "none yet"

## 3) Required Claude Code scaffolding

- [ ] `.claude/rules/` — exactly two files, both path-scoped:
  - `workflow-sprints.md` with `paths: ["docs/sprints/**"]`
  - `workflow-audits.md` with `paths: ["docs/audits/**"]`
- [ ] `.claude/skills/` — 17 skill directories, each with a `SKILL.md`

## 4) Skills (17 included)

- [ ] Confirm all 17 are present under `.claude/skills/<skill-name>/SKILL.md`
- [ ] Ensure **frontmatter `name` exactly matches the folder name**, lowercase and hyphenated

Pipeline: `new-idea`, `review-idea-doc`, `new-plan`, `check-plan`, `new-sprint`, `start-sprint`, `check-sprint`, `review-sprint`
Docs: `install-documentation`, `doc-audit`, `doc-sprint-sync`, `doc-write`
Audits: `mini-audit`, `in-depth-audit`
Utility: `sync`, `compound`, `verify-install`

- [ ] Add project-specific domain skills (3-6 is a healthy starting point)

## 5) Install Documentation (one-time canonical set — required)

- [ ] Run `/install-documentation` in Claude Code
  - Launches parallel sub-agents to explore the codebase: platform overview, schema/contracts, terminology
  - Creates populated (not placeholder) canonical docs
- [ ] Verify all canonical docs are populated (no bracket placeholders, no `YYYY-MM-DD` dates):
  - [ ] `docs/reference/PLATFORM_OVERVIEW_CANONICAL.md`
  - [ ] `docs/reference/SCHEMA_AND_CONTRACTS_CANONICAL.md`
  - [ ] `docs/reference/GLOBAL_TERMINOLOGY_INDEX_CANONICAL.md`
  - [ ] `docs/reference/DOCUMENTATION_INVENTORY.md`

## 6) Bootstrap the document tiers

- [ ] `docs/ideas/idea-template.md` — has Approaches Considered, Decision, and Sprint Sizing sections
- [ ] `docs/plans/plan-template.md` — has **no** Alternatives Considered section
- [ ] `docs/sprints/sprint-template.md` — has a Parallel Workstreams table
- [ ] Confirm `CLAUDE.md` states the one hard gate: plan-before-contract-change

## 7) Bootstrap drift control

- [ ] `docs/audits/templates/*` exist
- [ ] `docs/audits/active/` and `docs/audits/resolved/` exist
- [ ] Run a mini audit after the first non-trivial feature change

## 8) Optional: roadmap + knowledge compounding

- [ ] `docs/roadmap/ROADMAP.md` — fill in phases and deliverables, or delete `docs/roadmap/` if you won't use one
- [ ] `docs/solutions/` + template for the solutions library (`/compound`)

## 9) Verify install

- [ ] Run `/verify-install` and fix any missing items it reports.
- [ ] Confirm canonical docs are flagged **OK**, not UNPOPULATED. If any are unpopulated, re-run `/install-documentation`.
- [ ] Confirm it reports no stale artifacts (`.claude/agents/`, `.claude/hooks/`, `foundation.md`, `workflow-small-fixes.md`, `documentation-governance/`, `.mcp.json`, `start-session/`, `close-session/`, `skill-creator/`, `docs/sessions/`, and the four documentation-standards docs under `docs/reference/` — all removed from FBS).

## 10) First run

- [ ] Read `PROJECT_STATUS.md` and confirm it reads cleanly
- [ ] Pick a tier for your first piece of work — see the table in `CLAUDE.md`
