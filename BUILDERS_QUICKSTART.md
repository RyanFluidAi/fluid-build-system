---
doc_type: fbs_builders_quickstart
status: active
created: 2026-01-25
updated: 2026-08-06
---

# FBS for Builders (Non-Coders)

FBS (Fluid Build System) makes it possible to build confidently with AI agents by keeping the project organized and verifiable.

## The 4 things (simple language)

- **Rules**: permanent guardrails the AI must follow
  Location: `CLAUDE.md` + `.claude/rules/`
- **Playbooks**: small "how-to" guides the AI loads only when needed
  Location: `.claude/skills/`
- **Documents**: ideas, plans, and sprints — what we're thinking about, what we've decided, and what we're building
  Location: `docs/ideas/`, `docs/plans/`, `docs/sprints/`
- **Checks**: audits that confirm we didn't ship undocumented or drifting behavior
  Location: `docs/audits/`

## The 3 docs you always keep current

- `PROJECT_STATUS.md` — the fastest resume snapshot
- `docs/sprints/CURRENT_STATUS.md` — what's happening now
- `docs/sprints/<active sprint>.md` — the plan you're executing

## Pick a tier — this is the important part

Not every change needs the full process. Pick the smallest one that fits:

| Tier | What it looks like | What you do |
|------|-------------------|-------------|
| **1 — Small fix** | Fixing a typo, tweaking a button, one or two files | Just ask for it. No paperwork. |
| **2 — Sprint** | A real feature, but it doesn't change how data is shaped or how the API answers | `/new-sprint` → `/start-sprint` → `/review-sprint` |
| **3 — Plan + sprint** | It changes the database, the API, an integration, or a business rule | `/new-idea` → `/new-plan` → **you approve** → `/new-sprint` → `/start-sprint` → `/review-sprint` |

The **only** thing FBS will genuinely stop and wait for is Tier 3 approval. Everything else is a suggestion you can wave through.

## How the three documents differ

This is the part that trips people up:

- **Idea** — "here are three ways we could do this, and here's the one we're picking." Alternatives live here.
- **Plan** — "here is exactly what we are building." One direction, stated as fact. **No alternatives, no maybes.** One plan makes one sprint — if it's too big for one sprint, it becomes two plans.
- **Sprint** — the to-do list, split into tracks that several AI agents can work on at the same time.

Every idea, plan, and sprint gets a number (`IDEA-004`, `PLAN-002`, `SPRINT-007`). The AI tells you the number it assigned at the end of every run, so you always know what to refer back to.

## The skills you'll use most (type `/` in Claude Code)

To pick up where you left off, just read `PROJECT_STATUS.md` — there's no session skill to run.

- `/new-idea` — explores a new feature and lands on one approach
- `/new-plan` — writes the decision down for your approval (Tier 3 only)
- `/new-sprint` — creates the to-do list with parallel tracks
- `/start-sprint` — builds it
- `/review-sprint` — runs the tests and closes the sprint
- `/sync` — syncs your local repo with remote
- `/verify-install` — confirms FBS is installed correctly

Worth knowing, used less often:

- `/review-idea-doc` — checks an idea actually decided something
- `/check-plan` — pressure-tests a plan before you approve it
- `/check-sprint` — deep code review before closing a sprint
- `/mini-audit` — quick drift check after significant work
- `/doc-audit` — finds gaps between the code and the documentation
- `/doc-sprint-sync` — refreshes documentation after a sprint

## Can I have two sprints going at once?

Yes. FBS prefers that you finish one before starting the next, and it will say so — but it won't stop you. The warning is advice, not a lock.

## Install Documentation (one-time)

When FBS is first installed into a repo, run `/install-documentation` once. This launches sub-agents that explore the codebase and write reference documentation under `docs/reference/` based on what actually exists.

Then run `/verify-install` to confirm everything landed and no documents are still blank templates.

## Keeping documentation current

Documentation updates never block a sprint from closing. Two skills handle it when you decide it's worth doing:

- `/doc-audit` — compares the code against the docs and reports the gaps
- `/doc-sprint-sync` — updates the docs to match what a sprint just built
