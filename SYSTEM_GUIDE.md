---
doc_type: agent_build_system_guide
status: active
created: 2026-01-25
updated: 2026-02-25
---

# ABS — Agent Build System — Setup Guide (Portable)

This guide explains the **ABS (Agent Build System)** and how to reproduce it in a different project so an AI agent (in Claude Code) can build confidently with clear governance and low drift.

## Upstream compatibility note

During installation, check the current Claude Code docs for any changes since this kit was created:

- [CLAUDE.md and Memory](https://code.claude.com/docs/en/memory)
- [Skills](https://code.claude.com/docs/en/skills)
- [Subagents](https://code.claude.com/docs/en/sub-agents)
- [Hooks](https://code.claude.com/docs/en/hooks)
- [Settings](https://code.claude.com/docs/en/settings)
- [MCP Servers](https://code.claude.com/docs/en/mcp)

---

## 1) What ABS is

ABS is a **documentation + governance architecture** for AI-assisted development:

- **AI should not "infer" contracts** (schemas, API shapes, business rules). Those are **written canonically**.
- **AI should not load everything**. It should load a small set of **high-signal documents** and the **relevant skills**.
- **Work should be tracked** (current status + sprint doc), and drift should be caught early via **audits + fast checks**.

ABS is built from five pillars:

1. **Rules** (`.claude/rules/`): short workflow guardrails.
2. **Skills** (`.claude/skills/`): progressive-disclosure guidance by domain.
3. **Plans** (`docs/plans/`): approval workflow for contract-impacting changes.
4. **Sprints** (`docs/sprints/`): spec-driven execution and stage tracking.
5. **Audits** (`docs/audits/`): drift detection + remediation tracking.

---

## 2) System structure

### 2.1 The "resume fast" path

**In order**:

- `CLAUDE.md`: project instructions + governance rules (always loaded).
- `PROJECT_STATUS.md`: single-page resume context snapshot.
- `docs/sprints/CURRENT_STATUS.md`: what's happening now (active sprint, blockers, stage).
- `.claude/skills/`: load only relevant skills.
- `docs/audits/active/`: unresolved drift findings.

### 2.2 The execution loop (how features get built)

**Governance pipeline**:

```
/new-idea -> explore architecture and design
/review-idea-doc -> validate completeness
/new-plan -> formalize contract changes (requires approval)
/review-plan-doc -> validate before approval
  pause: wait for explicit approval
/new-sprint -> create agent-executable work plan
/review-sprint-doc -> validate before implementation
/start-sprint -> execute the work plan
/review-sprint -> verification + documentation + close-out
```

**Small fixes** skip the pipeline — follow `.claude/rules/workflow-small-fixes.md`.

### 2.3 Governance gates

**Plans** are required before implementing:
- API contract changes (field names/shapes, error envelopes, endpoint semantics)
- Database schema changes (tables/columns/constraints/migrations)
- Integration contract changes (webhooks, external service payloads)
- Canonical business rule changes

**Audits** are used to:
- detect drift between implementation and canonical docs
- verify new work didn't break governance rules
- create structured remediation plans (P0/P1 first)

---

## 3) Documentation rules

### 3.1 Documentation hierarchy

1. **Operational entrypoints**: `CLAUDE.md`, `PROJECT_STATUS.md`, `docs/sprints/CURRENT_STATUS.md`
2. **Canonical documentation**: platform architecture, schemas, API contracts, business rules
3. **Plans and decisions**: `docs/plans/`, `docs/ideas/`
4. **Drift tracking**: `docs/audits/`
5. **Progressive-disclosure guidance**: `.claude/skills/`
6. **Session logs**: `docs/sessions/`
7. **Solutions library**: `docs/solutions/`

### 3.2 What "canonical" means

Canonical documentation is authoritative. If canonical says "X" and code does "Y", that's drift.

- One canonical source per contract (no duplication).
- Versioned where it matters (minor vs major changes are explicit).
- Traceable: link to implementing code and tests when feasible.

### 3.3 Maintenance

- Edit canonical docs in place; use git history as the change log.
- If a canonical change is required, write a plan first.
- Don't create docs for one-off fixes; update snapshot docs instead.

---

## 4) Install Documentation (one-time)

A one-time process establishing the repo's canonical documentation system (via `/install-documentation`).

### 4.1 Required governance docs

- `docs/reference/DOCUMENTATION_STANDARDS_CANONICAL.md`
- `docs/reference/DOCUMENTATION_HIERARCHY_CANONICAL.md`

### 4.2 Initial canonical set

1. `docs/reference/PLATFORM_OVERVIEW_CANONICAL.md`
2. `docs/reference/SCHEMA_AND_CONTRACTS_CANONICAL.md`
3. `docs/reference/DOCUMENTATION_INVENTORY.md` (optional)
4. `docs/reference/GLOBAL_TERMINOLOGY_INDEX_CANONICAL.md`

### 4.3 "No invention" rule

Document what exists. Don't silently introduce new fields, behaviors, or contracts. Gaps follow the plan/approval workflow.

---

## 5) Ongoing Documentation (per sprint)

### 5.1 Required skill

Use the `documentation-governance` skill when updating canonical docs. It is configured as `user-invocable: false` so Claude loads it automatically when relevant.

### 5.2 Sprint Definition of Done

Before sprint stage moves to `done`:
- new behavior is documented
- new/changed schema/API/DB is documented with schema+example+semantics
- global terminology index is updated when new terms are introduced

### 5.3 Audit role

Mini and in-depth audits include a documentation completeness check.

---

## 6) Claude Code configuration system

### 6.1 CLAUDE.md hierarchy

Claude Code loads instructions from multiple scopes, merged at startup:

| Scope | File | Priority | Shared? |
|-------|------|----------|---------|
| User | `~/.claude/CLAUDE.md` | Lowest | No |
| Project | `CLAUDE.md` or `.claude/CLAUDE.md` | Middle | Yes (committed) |
| Local | `CLAUDE.local.md` | Highest | No (gitignored) |

Subdirectory `CLAUDE.md` files are loaded dynamically when Claude works in that directory. Use these for monorepo package-specific instructions.

### 6.2 Rules

Rules live in `.claude/rules/` as plain Markdown files. Unlike Cursor rules, Claude Code rules don't use `alwaysApply` or `globs` frontmatter — they are discovered as project context.

This kit includes three workflow rules:
- `workflow-small-fixes.md` — low-ceremony guardrails for small changes
- `workflow-sprints.md` — spec-driven execution with stage tracking
- `workflow-audits.md` — drift detection and remediation workflow

Foundation governance rules go in the root `CLAUDE.md` (always loaded).

### 6.3 Skills

Skills are the primary extension mechanism. Each skill is a directory under `.claude/skills/` containing a `SKILL.md` file.

**Discovery**: Claude loads skill descriptions into context at startup. Full content loads only when a skill is invoked (by user or Claude).

**Frontmatter fields**:

| Field | Purpose |
|-------|---------|
| `name` | Skill identifier (matches the `/name` invocation) |
| `description` | What it does and when to use it (primary trigger mechanism) |
| `argument-hint` | Autocomplete hint, e.g. `[sprint doc path]` |
| `disable-model-invocation` | `true` = only user can invoke via `/name` |
| `user-invocable` | `false` = hidden from `/` menu, Claude auto-loads when relevant |
| `context` | `fork` = runs in isolated subagent context |
| `agent` | Which subagent type when `context: fork` |
| `allowed-tools` | Auto-approve specific tools when this skill is active |

**Included skills** (19 total):

Governance pipeline:
- `new-idea` — explore architecture, create idea artifact
- `review-idea-doc` — validate idea before it becomes a plan
- `new-plan` — formal contract-change plan
- `review-plan-doc` — validate plan before approval
- `new-sprint` — create agent-executable sprint doc
- `review-sprint-doc` — validate sprint doc before implementation
- `start-sprint` — execute work plan (implementation only)
- `review-sprint` — verification gates + close-out

Session management:
- `start-session` — review project status at session start
- `close-session` — end-of-session resume-fast checklist

Audits:
- `mini-audit` — targeted drift check
- `in-depth-audit` — comprehensive subsystem audit

Documentation:
- `documentation-governance` — canonical doc update rules (background knowledge)
- `install-documentation` — one-time canonical doc setup

Code quality:
- `commit` — structured git commit with conventional message format
- `review-code` — code review for quality, correctness, and conventions

Utility:
- `deploy-app` — deployment workflow
- `compound` — document solved problems
- `skill-creator` — meta-skill for creating new skills
- `verify-install` — verify ABS installation

### 6.4 Agents (subagents)

Agents are specialized Claude instances with their own system prompts, tool restrictions, and model selection. Each is a Markdown file in `.claude/agents/`.

**Frontmatter fields**:

| Field | Purpose |
|-------|---------|
| `name` | Unique identifier |
| `description` | What it does and when to delegate |
| `model` | `haiku`, `sonnet`, `opus`, or `inherit` |
| `tools` | Comma-separated tool list (e.g. `Read, Glob, Grep, Bash`) |
| `maxTurns` | Maximum agentic turns |
| `memory` | Persistent memory scope: `user`, `project`, or `local` |
| `permissionMode` | `default`, `acceptEdits`, `dontAsk`, `bypassPermissions`, `plan` |
| `skills` | Skills to preload into agent context |
| `isolation` | `worktree` for git worktree isolation |
| `background` | `true` to always run as background task |

**Included agents**: `verifier`, `test-runner`, `debugger`

### 6.5 Hooks

Hooks are deterministic automation — shell commands or LLM evaluators that run at specific lifecycle points. Configured in `.claude/settings.json`.

**Key hook events**:

| Event | When | Use case |
|-------|------|----------|
| `PreToolUse` | Before a tool call | Block risky commands, protect secret files |
| `PostToolUse` | After a tool call | Auto-format edited files |
| `SessionStart` | Session starts/resumes | Re-inject context after compaction |
| `Stop` | Claude finishes responding | Verify task completion |

**Three hook types**:
- `command` — run a shell command
- `prompt` — single-turn LLM evaluation (returns ok/not ok)
- `agent` — multi-turn LLM verification with tool access

**Included hooks**:
- `shell-guard.sh` — block risky shell commands (PreToolUse)
- `read-guard.sh` — block reading secret files (PreToolUse)
- `format.sh` — auto-format after edits (PostToolUse)
- Compaction re-injection (SessionStart)
- Status doc update reminder (Stop)

### 6.6 Settings

`.claude/settings.json` configures hooks, permissions, and environment variables.

```json
{
  "permissions": {
    "allow": ["Read", "Glob", "Grep"],
    "deny": []
  },
  "hooks": { ... },
  "env": { ... }
}
```

Personal overrides go in `.claude/settings.local.json` (gitignored).

### 6.7 MCP Servers

`.mcp.json` at the project root configures shared MCP (Model Context Protocol) server connections. Supports environment variable expansion.

---

## 7) Plans: governance gate for contract changes

### 7.1 When plans are required

- schemas, API contracts, integration contracts, business rules

Not required for:
- purely internal refactors
- bug fixes restoring compliance with canonical spec

### 7.2 Plan workflow

1. Explore via `/new-idea` (optional)
2. Review idea via `/review-idea-doc` (optional)
3. Create plan via `/new-plan`
4. Review plan via `/review-plan-doc`
5. Wait for explicit approval
6. Create sprint via `/new-sprint`
7. Implement and update canonical docs
8. Mark plan as "Implemented"

---

## 8) Sprints: spec-driven execution

### 8.1 Current status

`docs/sprints/CURRENT_STATUS.md` is the single source of truth for what sprint is active, its stage, and blockers. Update it whenever priorities or blockers change.

### 8.2 Sprint docs

Required fields: stage (`planning | in_progress | verification | done`), acceptance criteria, test plan, links to plans (if contracts change).

### 8.3 Stage progression

```
planning -> in_progress -> verification -> done
```

- `/start-sprint` handles planning -> in_progress
- `/review-sprint` handles verification -> done (the only way to reach done)

---

## 9) Audits: drift detection

### 9.1 Mini audit

After cross-cutting changes, contract touchpoint changes, or suspected drift. Goal: confirm critical points match canonical docs.

### 9.2 In-depth audit

Monthly or at major milestones. Goal: comprehensive verification + remediation plan.

### 9.3 Lifecycle

- Write to `docs/audits/active/`
- At most one active mini and one active in-depth audit at a time
- Move to `docs/audits/resolved/YYYY-MM/` when resolved

---

## 10) Session management

### 10.1 Starting a session

Use `/start-session` to review project status, active sprint, and blockers. Provides immediate "resume fast" context.

### 10.2 Closing a session

Use `/close-session` to commit changes, write a session log, update status docs, and ensure the repo is ready for the next session.

Session logs go in `docs/sessions/YYYY-MM-DD.md` (append-only, 1-10 bullets).

---

## 11) Knowledge compounding

### 11.1 Solutions library

Use `/compound` after solving a non-obvious problem to create a solution entry under `docs/solutions/` with:
- Symptom, root cause, fix, prevention step
- Solutions that become reusable procedures are promoted to skills

### 11.2 Skill creation

Use `/skill-creator` (or the skill-creator reference docs) when creating new skills. Follow progressive disclosure: keep SKILL.md under 500 lines, move detailed reference material to supporting files.

---

## 12) Installing into an existing project

### Phase A — Minimal "resume fast"

- Add `CLAUDE.md` to the repo root
- Add `PROJECT_STATUS.md`
- Add `docs/sprints/CURRENT_STATUS.md`

### Phase B — Governance gates

- Add `docs/plans/` with plan template
- Add `docs/ideas/` with idea template
- Update `CLAUDE.md` to require plans for contract-impacting changes

### Phase C — Skills

- Add `.claude/skills/documentation-governance/`
- Create 3-6 initial domain skills

### Phase D — Execution tracking

- Add `docs/sprints/SPRINTS/` with sprint template
- Start tracking work using sprint docs with stage progression

### Phase E — Drift control

- Add `docs/audits/` with templates
- Run a first mini audit after the first non-trivial feature

### Phase F — Automation

- Add `.claude/agents/` for verification/debug/testing workflows
- Add `.claude/settings.json` with hooks for formatting and safety gates
- Add `.mcp.json` for shared tool integrations

### Phase G — Session management

- Add `/start-session` and `/close-session` skills
- Add `docs/sessions/` for session logs
- Add `docs/solutions/` for knowledge compounding
