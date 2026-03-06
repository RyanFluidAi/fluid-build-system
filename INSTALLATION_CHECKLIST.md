---
doc_type: abs_installation_checklist
status: active
created: 2026-01-25
updated: 2026-02-25
---

# ABS (Agent Build System) — Installation Checklist (1 page)

Use this checklist when installing into a **new** or **existing** repo.

## 0) Confirm upstream Claude Code conventions (do first)

- [ ] Review: [CLAUDE.md and Memory](https://code.claude.com/docs/en/memory)
- [ ] Review: [Skills](https://code.claude.com/docs/en/skills)
- [ ] Review: [Subagents](https://code.claude.com/docs/en/sub-agents)
- [ ] Review: [Hooks](https://code.claude.com/docs/en/hooks)
- [ ] Review: [Settings](https://code.claude.com/docs/en/settings)
- [ ] Review: [MCP Servers](https://code.claude.com/docs/en/mcp)

## 1) Copy the starter tree

- [ ] Copy everything under `AGENT_BUILD_SYSTEM/templates/project-root/` into the destination repo root.

## 2) Required "resume fast" docs (fill immediately)

- [ ] `CLAUDE.md` (project instructions + governance rules)
- [ ] `PROJECT_STATUS.md`
- [ ] `docs/sprints/CURRENT_STATUS.md`

## 3) Required Claude Code scaffolding (must exist)

- [ ] `.claude/rules/` (workflow rules)
- [ ] `.claude/skills/` (skills are discovered here by Claude Code)
- [ ] `.claude/agents/` (subagent definitions)
- [ ] `.claude/settings.json` (hooks + permissions)
- [ ] `.mcp.json` (MCP server connections, if needed)

## 4) Install Documentation (one-time canonical set)

- [ ] Create/confirm the documentation governance docs:
  - [ ] `docs/reference/DOCUMENTATION_STANDARDS_CANONICAL.md`
  - [ ] `docs/reference/DOCUMENTATION_HIERARCHY_CANONICAL.md`
- [ ] Create the initial canonical set:
  - [ ] **Start**: `docs/reference/PLATFORM_OVERVIEW_CANONICAL.md`
  - [ ] `docs/reference/SCHEMA_AND_CONTRACTS_CANONICAL.md`
  - [ ] (Optional) `docs/reference/DOCUMENTATION_INVENTORY.md`
  - [ ] **End**: `docs/reference/GLOBAL_TERMINOLOGY_INDEX_CANONICAL.md`
- [ ] Run `/install-documentation` to guide this step

## 5) Bootstrap skills (minimum 3-6)

- [ ] Create 3-6 skills under `.claude/skills/<skill-name>/SKILL.md`
- [ ] Ensure **frontmatter `name` exactly matches folder name** and is lowercase/hyphenated
- [ ] Include `documentation-governance` skill (required)

## 6) Bootstrap governance (ideas + plans)

- [ ] `docs/ideas/README.md`
- [ ] `docs/plans/README.md` + `plan-template.md`
- [ ] Confirm `CLAUDE.md` enforces plan-before-contract-change

## 7) Bootstrap execution tracking (sprints)

- [ ] `docs/sprints/README.md`
- [ ] `docs/sprints/sprint-template.md`

## 8) Bootstrap drift control (audits + fast checks)

- [ ] `docs/audits/templates/*` exist
- [ ] Run a mini audit after the first non-trivial feature change
  - [ ] Include "documentation completeness" check

## 9) Verify install

- [ ] In Claude Code, type `/verify-install` and fix any missing items it reports.

## 10) Enable hooks and agents

- [ ] Hooks: configure `.claude/settings.json` with hook scripts
  - Make scripts executable: `chmod +x .claude/hooks/*.sh`
  - Keep `shell-guard.sh` and `read-guard.sh` conservative; tune to your environment
- [ ] Agents: keep `.claude/agents/` in version control
  - Use `verifier` after marking work "done"
  - Use `test-runner` after code changes
  - Use `debugger` when failures occur

## 11) Optional: session management + knowledge compounding

- [ ] Add `/start-session` and `/close-session` to your workflow
- [ ] Create `docs/sessions/` for session logs
- [ ] Create `docs/solutions/` for the solutions library (`/compound`)
