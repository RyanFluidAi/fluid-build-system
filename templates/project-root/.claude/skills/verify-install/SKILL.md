---
name: verify-install
description: Verify FBS installation is complete and correctly configured. Use after installing FBS into a repo to confirm everything is in place.
---

# /verify-install — Verify FBS Installation

## Goal

Check that all FBS components are properly installed and report any missing items.

## Steps

### 1) Check required files

Verify these files exist and are non-empty:

**Required:**
- [ ] `CLAUDE.md` (root project instructions)
- [ ] `.claude/agents/` directory with at least one agent definition
- [ ] `PROJECT_STATUS.md` (resume-fast snapshot)
- [ ] `docs/sprints/CURRENT_STATUS.md` (current work status)

**Rules:**
- [ ] `.claude/rules/foundation.md`
- [ ] `.claude/rules/workflow-small-fixes.md`
- [ ] `.claude/rules/workflow-sprints.md`
- [ ] `.claude/rules/workflow-audits.md`

**Skills (minimum set):**
- [ ] `.claude/skills/documentation-governance/SKILL.md`
- [ ] At least 2 additional domain skills

### 2) Check recommended components

**Settings:**
- [ ] `.claude/settings.json` (hooks and permissions)
- [ ] `.mcp.json` (MCP server configuration)

**Agents:**
- [ ] `.claude/agents/` directory exists with at least one agent definition

**Documentation structure:**
- [ ] `docs/plans/` directory exists with plan template
- [ ] `docs/ideas/` directory exists with idea template
- [ ] `docs/sprints/sprint-template.md` exists
- [ ] `docs/audits/active/` and `docs/audits/resolved/` directories exist
- [ ] `docs/sessions/` directory exists

### 3) Check canonical documentation (critical)

Verify `docs/reference/` exists and canonical docs are **populated** (not just placeholder templates).

**Required canonical docs:**
- [ ] `docs/reference/DOCUMENTATION_STANDARDS_CANONICAL.md`
- [ ] `docs/reference/DOCUMENTATION_HIERARCHY_CANONICAL.md`
- [ ] `docs/reference/PLATFORM_OVERVIEW_CANONICAL.md`
- [ ] `docs/reference/SCHEMA_AND_CONTRACTS_CANONICAL.md`
- [ ] `docs/reference/GLOBAL_TERMINOLOGY_INDEX_CANONICAL.md`
- [ ] `docs/reference/DOCUMENTATION_INVENTORY.md`

**Placeholder detection:** For each file above, check whether it still contains placeholder markers indicating it was never populated. A doc is **unpopulated** if it contains any of:
- `YYYY-MM-DD` in frontmatter dates
- Bracket placeholders like `[definition]`, `[What this platform is]`, `[primary user types]`
- Ellipsis `…` as the sole content of a section
- Template markers like `[present/missing]`, `[term]`, `[Name]`

**Reporting:**
- If `docs/reference/` is missing entirely: report as **ACTION REQUIRED — run `/install-documentation`**
- If files exist but are unpopulated: report each one as **UNPOPULATED — run `/install-documentation` to generate real content from the codebase**
- If files exist and are populated: report as **OK**

### 4) Check hooks

- [ ] `.claude/hooks/` directory exists with hook scripts
- [ ] Hook scripts are executable (`chmod +x`)

### 5) Report results

Provide a structured summary:
- **Required**: pass/fail per item
- **Recommended**: present/missing per item
- **Canonical docs**: OK / UNPOPULATED / MISSING per item
- **Next steps**: what to do about missing items (always recommend `/install-documentation` if any canonical docs are unpopulated)
