---
doc_type: agent_build_system
status: active
created: 2026-01-25
updated: 2026-02-25
---

# ABS — Agent Build System (Claude Code)

This folder is a **portable starter kit** for setting up the **ABS (Agent Build System)**, so AI agents (and humans) can build confidently with:

- **Progressive disclosure** (skills, not giant rules)
- **One-source-of-truth status** (fast resume)
- **Governance pipeline** (idea -> plan -> approval -> sprint -> review)
- **Spec-driven execution** (sprints with stage tracking)
- **Drift control** (mini + in-depth audits)
- **Session management** (start/close session, knowledge compounding)

## What you get here

- `BUILDERS_QUICKSTART.md`: one-page explanation for non-coders.
- `SYSTEM_GUIDE.md`: end-to-end explanation of what the system is, how it's structured, and how to maintain it.
- `INSTALLATION_CHECKLIST.md`: one-page install checklist (new or existing repos).
- `templates/project-root/`: a **copy/paste** directory tree you can drop into a new project (CLAUDE.md, skills, agents, hooks, rules, plans, sprints, audits, and starter canonical doc stubs).

## Start here (recommended order)

1. Read `BUILDERS_QUICKSTART.md` (simple mental model and the skills you'll use).
2. Follow `INSTALLATION_CHECKLIST.md` (step-by-step install into a repo).
3. Use `SYSTEM_GUIDE.md` when you need deeper reference or want to extend ABS.

## Quick start (port to another repo)

0. Check the upstream Claude Code docs for any changes since this kit was created:

   - [CLAUDE.md and Memory](https://code.claude.com/docs/en/memory)
   - [Skills](https://code.claude.com/docs/en/skills)
   - [Subagents](https://code.claude.com/docs/en/sub-agents)
   - [Hooks](https://code.claude.com/docs/en/hooks)
   - [Settings](https://code.claude.com/docs/en/settings)
   - [MCP Servers](https://code.claude.com/docs/en/mcp)

1. Copy the template tree into the new repo root:

   - Copy everything under `AGENT_BUILD_SYSTEM/templates/project-root/` into the destination repo root.
   - Then edit the template files to match the project name, architecture, and governance.

2. Fill the "resume fast" docs:

   - `CLAUDE.md` (project instructions + governance rules)
   - `PROJECT_STATUS.md`
   - `docs/sprints/CURRENT_STATUS.md`

3. Create the first 3-6 skills in `.claude/skills/` (keep each `SKILL.md` focused).

4. Run `/verify-install` to confirm everything is in place.

5. Run your first **mini audit** after the first non-trivial feature.

## Adopting into an existing repo

If you're installing into a repo that already has docs and processes, do it incrementally:

- **Phase A**: Add `CLAUDE.md`, `PROJECT_STATUS.md`, `docs/sprints/CURRENT_STATUS.md` (resume fast).
- **Phase B**: Add `docs/plans/` and `docs/ideas/` (governance gates).
- **Phase C**: Add `.claude/skills/` once you know your recurring domains.
- **Phase D**: Add sprint tracking with `docs/sprints/`.
- **Phase E**: Add audits with `docs/audits/`.
- **Phase F**: Add `.claude/agents/`, `.claude/settings.json` (hooks), and `.mcp.json`.
- **Phase G**: Add session management (`/start-session`, `/close-session`) and knowledge compounding (`/compound`).

See `SYSTEM_GUIDE.md` for the phased rollout detail.

## Design goals

- **Low token**: don't load the entire repo context; load only relevant skills and the current status doc.
- **Stable governance**: contract changes are proposed and approved before implementation.
- **High confidence**: audits and fast checks prevent drift and undocumented behavior.
- **Resume fast**: any session can pick up where the last one left off via status docs and session logs.
