# Agent Build System — Maintainer Instructions

This repo is the **ABS template kit**. Its sole purpose is maintaining the portable template under `templates/project-root/` that gets copied into target projects.

## Repo structure

- `README.md` — overview and quick start for users
- `BUILDERS_QUICKSTART.md` — non-coder one-pager
- `SYSTEM_GUIDE.md` — full reference guide
- `INSTALLATION_CHECKLIST.md` — step-by-step install checklist
- `templates/project-root/` — the copy/paste template tree (this is the product)

## Rules for editing this repo

- **Template is the product.** All changes to `.claude/`, `docs/`, skills, agents, hooks, and rules should happen inside `templates/project-root/`.
- **Keep docs in sync.** If you change template structure (add/remove/rename files), update all four guide docs: `README.md`, `BUILDERS_QUICKSTART.md`, `SYSTEM_GUIDE.md`, `INSTALLATION_CHECKLIST.md`.
- **Use "skills" not "commands".** When referring to `/slash` invocations, use the term "skill". Reserve "command" for shell commands and hook types only.
- **No AGENTS.md.** Agent operating instructions belong in `CLAUDE.md`. Agent definitions live in `.claude/agents/`. There is no separate `AGENTS.md` file.
- **Hook scripts require `jq`.** All hook scripts include a `jq` availability guard. Maintain this pattern for new hooks.
- **Keep the `.gitignore` current.** The template `.gitignore` at `templates/project-root/.gitignore` should cover OS files, editor artifacts, secrets, and Claude Code local overrides.

## Upstream compatibility

Before making changes, check current Claude Code docs for convention updates:
- [CLAUDE.md and Memory](https://code.claude.com/docs/en/memory)
- [Skills](https://code.claude.com/docs/en/skills)
- [Subagents](https://code.claude.com/docs/en/sub-agents)
- [Hooks](https://code.claude.com/docs/en/hooks)
- [Settings](https://code.claude.com/docs/en/settings)
- [MCP Servers](https://code.claude.com/docs/en/mcp)
