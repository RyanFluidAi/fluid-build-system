---
project: your-project-name
status: active
---

# Agent Operating Manual

This repository uses the **Fluid Build System (FBS)** — a governance framework for AI-assisted development with progressive disclosure, approval gates, and drift control.

## Resume fast (read in order)

1. `PROJECT_STATUS.md` — fastest snapshot of current state
2. `docs/sprints/CURRENT_STATUS.md` — what's happening now
3. `.claude/skills/` — load only the relevant skills (2-4 max)
4. `.claude/agents/` — specialized subagents (verifier, test-runner, debugger, + 6 reviewers for --deep mode)
5. `docs/audits/active/` — unresolved P0/P1 drift findings

## Governance (non-negotiable)

- If a change impacts canonical contracts (schemas/API/DB/business rules), create a **plan** in `docs/plans/` and wait for explicit approval before implementing.
- Contract changes follow the full pipeline:

```
/new-idea          → explore architecture and design
/review-idea-doc   → validate idea completeness
/new-plan          → formalize contract changes
/check-plan        → deep feasibility review against codebase
  ⏸ wait for explicit user approval
/new-sprint        → create agent-executable work plan (self-validates)
/start-sprint      → execute the work plan
/check-sprint      → deep code review after implementation
/review-sprint     → verification gates + close-out → stage: done
/doc-sprint-sync   → (optional, manual) refresh canonical docs in docs/reference/
```

- `/review-sprint` supports `--deep` for parallel reviewer subagents (architecture, security, performance, data integrity, test quality, docs governance)
- Documentation sync is **not** a sprint-close gate. Sprints close on verification; canonical docs are updated manually post-close via `/doc-sprint-sync`.

## Workflows

- **Small fixes** (1-3 files): follow `.claude/rules/workflow-small-fixes.md`
- **Sprints** (planned work): follow `.claude/rules/workflow-sprints.md`
- **Audits** (drift checks): follow `.claude/rules/workflow-audits.md`

## Progressive disclosure

- Load only what you need. Don't read the entire repo.
- Skills are loaded on demand — descriptions are always in context, full content loads when invoked.
- Keep context lean: prefer 2-4 relevant skills over broad repo-wide reading.

## Session checkpoints

At the start of meaningful work:
- State which status docs you read (`PROJECT_STATUS.md`, `docs/sprints/CURRENT_STATUS.md`).
- State which skill(s) you loaded.

At the end of a session (or use `/close-session`):
- State whether a new skill should be created/updated.
- State whether an audit, plan, or roadmap update is required.

## Documentation (four skills, decoupled from sprint close)

- **`/install-documentation`** (one-time) — establishes the repo's canonical documentation set under `docs/reference/`.
- **`/doc-audit`** (on demand) — scans the codebase against existing canonical docs and produces a gap report.
- **`/doc-sprint-sync`** (manual, post-sprint) — refreshes canonical docs to reflect what a sprint built. Not invoked by `/review-sprint`.
- **`/doc-write`** (internal) — writing standards loaded by the other doc skills.
- **`documentation-governance`** (reference) — high-level non-negotiables, pointers to the four skills above.

## Keep documentation minimal

- Don't create new docs for one-off fixes.
- Prefer updating the single snapshot (`PROJECT_STATUS.md`) + existing sprint/audit docs.
