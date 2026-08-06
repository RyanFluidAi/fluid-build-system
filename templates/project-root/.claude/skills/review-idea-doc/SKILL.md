---
name: review-idea-doc
description: Pressure-test an idea doc against the codebase before it becomes a plan. Checks whether the chosen direction is actually viable here, whether the rejected options were rejected for real reasons, and whether the scope claim holds up. Read-only.
argument-hint: "[path to idea doc under docs/ideas/]"
context: fork
background: false
allowed-tools: Read, Glob, Grep
disallowed-tools: Edit, Write, NotebookEdit
---

# /review-idea-doc — Idea Review

## Goal

Decide one thing: **is this idea ready to become a plan?**

An idea is ready when it has picked one direction, that direction is viable in *this* codebase, and the work is sized honestly. Everything else is secondary.

This is a technical review, not a proofread. Read the code the idea proposes to change. An idea that is beautifully formatted and architecturally wrong should fail this review; one with a sloppy Constraints section but a sound, verified direction should pass.

**Do NOT create, write, or modify any files. Report findings in the chat only.**

## Inputs

$ARGUMENTS

This skill runs in a forked context and cannot see the conversation that invoked it. If no path is provided, review the most recently created `status: draft` idea doc under `docs/ideas/` and say which one you picked. Do not ask a follow-up question — there is no one to answer it.

## Steps

### 1) Load

- Read the idea doc.
- Read `CLAUDE.md` for project conventions.
- Read `docs/reference/PLATFORM_OVERVIEW_CANONICAL.md` and `SCHEMA_AND_CONTRACTS_CANONICAL.md` if they exist — the idea must be consistent with documented contracts.

### 2) Is there a decision? (blocking)

Find the **Decision** section.

- If it is missing, empty, or leaves two options live: **stop the assessment here and report "Not ready — no decision."** Everything downstream depends on knowing what is being built. A plan cannot be written from an undecided idea.
- Verify the Architecture/Approach section describes the *chosen* option and not a blend of several. A blend is the most common failure — the author picks Option A, then quietly writes up A-plus-half-of-B.
- Verify every option in Approaches Considered is explicitly either chosen or rejected.

### 3) Is the chosen direction viable here? (the core of the review)

This requires reading actual code. For each concrete claim in the Architecture/Approach section:

- **Does the thing it builds on exist?** Grep for the tables, endpoints, modules, and services the approach depends on. Flag anything referenced that isn't there.
- **Does the codebase already do this?** Search for existing implementations of the same capability. An idea that reinvents something already present is the highest-value catch in this whole review.
- **Does it fit the established patterns?** Compare against how similar features are built here. Flag an approach that would be the first of its kind — that may be fine, but it should be a deliberate choice, not an accident.
- **Does it contradict a documented contract?** Check the canonical docs. An idea that requires changing a documented contract is Tier 3 and must say so.
- **What does it break?** Grep for consumers of anything the approach modifies or removes. Flag consumers the idea doesn't mention.

### 4) Were the rejected options rejected for real reasons?

Read the rejection rationale for each discarded option against what you found in step 3.

- Flag any option rejected on a premise the codebase contradicts ("rejected because we don't have X" when X exists).
- Flag any option that now looks *better* than the chosen one given what you found. Say so plainly, once, with evidence — then let the author decide. Do not relitigate a sound decision on taste.
- If only one option was ever considered on a genuinely forked problem, note it.

### 5) Does the scope claim hold up?

Read the **Sprint Sizing** section against the work you traced in step 3.

- Count the surfaces the approach actually touches: schema, endpoints, UI, integrations, migrations.
- If it claims "fits one sprint" but touches many surfaces, say so and propose where the split falls — each piece becomes its own plan and its own sprint.
- If it proposes a split, check the pieces are independently shippable. A split that leaves the system broken between pieces isn't a split.

### 6) Unresolved items

- List every Open Question. Each one blocks `/new-plan`, because a plan cannot contain open questions.
- Flag hedging in the Summary, Decision, Architecture, or Constraints sections: "maybe", "TBD", "not sure", "possibly", "might", "to be decided", "unclear", "need to figure out". Uncertainty is expected in Open Questions and Approaches Considered — ignore it there.
- Check Constraints against the chosen approach. Flag any approach that violates a stated constraint.
- Check Non-goals against the approach. Flag anything listed as out of scope that the approach quietly includes.

### 7) Housekeeping (report as one line, not a section)

Check frontmatter in one pass: `doc_type: idea`, `number` matching the filename's `NNN`, `status` one of `draft`/`accepted`/`parked`/`rejected`, valid `created`/`last_updated` dates, non-empty `owner`, and any files named in `related_plans`/`related_sprints` actually existing.

Report as a single pass/fail line with the specific problems. These are clerical and should never be the headline of a review.

## Output

Report in the chat:

### Verdict
**Ready for `/new-plan`** | **Not ready — no decision** | **Not ready — direction not viable** | **Ready with caveats**

One paragraph explaining the verdict.

### Decision
- Chosen direction, restated in one line
- Options left unresolved: none / list them
- Approach matches the chosen option: yes / it's a blend of X and Y

### Viability (evidence required)
| Claim in the idea | Verified against | Result |
|---|---|---|
| ... | `path/to/file.ts:42` | Confirmed / Doesn't exist / Already implemented / Conflicts with pattern |

- **Already exists**: anything the codebase already does
- **Breaks**: consumers the idea doesn't account for
- **Contract impact**: which canonical contracts change (determines Tier 2 vs Tier 3)

### Rejected options
- Any rejected on a false premise, with the evidence
- Any that now look stronger than the chosen one (state once, don't push)

### Scope
- Surfaces touched: schema / API / UI / integrations / migrations
- Sizing claim: credible / optimistic — proposed split into N plans

### Blockers before `/new-plan`
1. ...

### Housekeeping
One line: frontmatter and section completeness, pass/fail with specifics.
