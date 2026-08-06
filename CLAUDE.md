# Fluid Build System — Maintainer Instructions

This repo is the **FBS template kit**. Its sole purpose is maintaining the portable template under `templates/project-root/` that gets copied into target projects.

## Repo structure

- `README.md` — overview and quick start for users
- `BUILDERS_QUICKSTART.md` — non-coder one-pager
- `SYSTEM_GUIDE.md` — full reference guide
- `INSTALLATION_CHECKLIST.md` — step-by-step install checklist
- `templates/project-root/` — the copy/paste template tree (this is the product)

## Rules for editing this repo

- **Template is the product.** All changes to `.claude/`, `docs/`, skills, and rules should happen inside `templates/project-root/`.
- **Keep docs in sync.** If you change template structure (add/remove/rename files), update all four guide docs: `README.md`, `BUILDERS_QUICKSTART.md`, `SYSTEM_GUIDE.md`, `INSTALLATION_CHECKLIST.md` — and the skill inventory in `verify-install`, which is the kit's own conformance check.
- **Use "skills" not "commands".** When referring to `/slash` invocations, use the term "skill". Reserve "command" for shell commands.
- **No AGENTS.md.** Agent operating instructions belong in `CLAUDE.md`. There is no separate `AGENTS.md` file.
- **Ship no hooks, no agents, no `settings.json`.** FBS is documentation and skills only. It must not intercept tool calls, define custom subagents, or impose a permissions model — those are the target project's decisions. Parallelism comes from the sprint doc's workstream table driving general-purpose sub-agents.
- **One hard gate.** Plan-before-contract-change is the only thing FBS blocks on. Resist adding gates; prefer advisory warnings that let work continue.
- **Plans are singular.** The plan template must never regain an "Alternatives Considered" section, and a plan must never spawn more than one sprint. Alternatives belong in idea docs.
- **Keep the `.gitignore` current.** The template `.gitignore` at `templates/project-root/.gitignore` should cover OS files, editor artifacts, secrets, and Claude Code local overrides.

## Skill frontmatter traps

Two mistakes this kit has already made once — don't reintroduce them:

- `allowed-tools` **pre-approves**, it does not restrict. A "read-only" skill needs `disallowed-tools: Edit, Write, NotebookEdit`.
- `context: fork` defaults to `background: true`. Any forked skill whose output gates a decision needs `background: false`, and must never end a step with "ask the user" — a fork has no conversation to ask into.

## Upstream compatibility

Before making changes, check current Claude Code docs for convention updates:
- [CLAUDE.md and Memory](https://code.claude.com/docs/en/memory)
- [Skills](https://code.claude.com/docs/en/skills)
- [Subagents](https://code.claude.com/docs/en/sub-agents)
- [Settings](https://code.claude.com/docs/en/settings)
