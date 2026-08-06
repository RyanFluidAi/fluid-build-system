---
name: new-idea
description: Explore new functionality architecture through discussion and create an idea artifact under docs/ideas/. Use when exploring a feature concept, discussing implementation approaches, or designing new capabilities before committing to a plan.
argument-hint: "[feature or concept to explore]"
---

# /new-idea — Explore new functionality architecture (discussion + idea artifact)

## Goal

Facilitate a **discussion-only session** to explore and design new functionality before creating a formal plan.

Creates a durable idea artifact under `docs/ideas/` so the idea can be revisited without re-deriving context.

No runtime code changes will be made during this skill.

## Inputs

$ARGUMENTS

If no topic is provided, ask the user to describe the functionality they want to build.

## Steps

### Phase 1: Discovery

- Ask the user to describe the functionality they want to build
- Ask clarifying questions about:
  - Who will use it (user roles, permissions)
  - How it relates to existing features
  - Expected user workflows
  - Data that needs to be stored/displayed
  - Any external integrations needed

### Phase 2: Codebase analysis

- Search for similar patterns in the codebase
- Review relevant schema files
- Check existing API routes
- Look at similar UI implementations
- Review existing capabilities in relevant areas
- Check canonical documentation and identify what new docs would be required

### Phase 3: Architecture recommendations

Propose a high-level design covering:
- **Database layer**: tables, relationships, migrations approach
- **API layer**: endpoints, validation, auth middleware
- **UI layer**: pages, components, routing strategy
- **Integrations**: external services, webhooks, third-party APIs
- **Permissions**: role-based access control approach

### Phase 4: Trade-offs discussion

**This is the only stage in FBS where alternatives belong.** Explore them properly here, because the plan that follows will state a single direction and carry none of this forward.

- Lay out the genuine approaches with honest pros and cons
- Identify dependencies and prerequisites
- Note what would make you change your mind

### Phase 5: Decision

Drive the discussion to a **single chosen direction**, and get the user to confirm it. An idea doc that ends with two live options isn't finished — the plan can't be written from it.

- State the chosen architecture
- Record why the alternatives were rejected (this stays in the idea doc)
- Estimate whether the work fits **one sprint**. If it clearly doesn't, note where it should be split — each split becomes its own plan and its own sprint.
- List remaining open questions explicitly

### Phase 6: Create the idea artifact (required)

- **Determine the next idea number**: scan `docs/ideas/` for existing `IDEA-NNN-*.md` files and use the next sequential number (zero-padded to 3 digits). If no ideas exist, start at `IDEA-001`.
- Create `docs/ideas/IDEA-NNN-YYYY-MM-DD-<short-topic>.md` using `docs/ideas/idea-template.md`
  - `NNN` = next sequential number (e.g., `001`, `002`, `003`)
  - `YYYY-MM-DD` = today's date
  - `<short-topic>` = kebab-case, 3-6 words
- Set `status: draft` unless the user explicitly says the idea is accepted
- Set `number: NNN` in frontmatter
- Link candidate canonical docs (do not edit canonical docs from an idea; use a plan when required)

## Output

Report, in this order:

1. **`IDEA-NNN` created at `docs/ideas/IDEA-NNN-YYYY-MM-DD-<topic>.md`** — lead with the assigned number and full path.
2. **Chosen direction** — the single architecture decided on, and the rejected alternatives in one line each.
3. **Sprint sizing** — fits one sprint, or where it should be split into multiple plans.
4. **Open questions** — anything still unresolved.
5. **Next step** — `/new-plan` if contracts change (Tier 3), or `/new-sprint` directly if not (Tier 2).

## Important notes

- **NO runtime code changes** during this skill — discussion and a documentation artifact only
- Reference existing patterns with code citations
- Be thorough but concise — clarity over exhaustiveness
- If the idea touches core contracts (auth, billing, org model), flag that early
- Keep the user engaged — this is a collaborative discussion, not a lecture
- Alternatives explored here stay here. `/new-plan` will carry forward only the decision.
