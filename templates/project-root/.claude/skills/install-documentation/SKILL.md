---
name: install-documentation
description: Install the canonical documentation set (one-time during FBS setup). Uses sub-agents to explore the codebase and create comprehensive source-of-truth reference docs under docs/reference/.
---

# /install-documentation — Install Canonical Documentation Set

## Goal

Create or confirm the canonical documentation set under `docs/reference/`. This is a one-time setup during FBS installation. Sub-agents explore the actual codebase so each canonical doc reflects reality, not placeholders.

## Steps

### 1) Pre-flight check

- Check `docs/reference/` for existing canonical docs.
- For each file, check whether it is a populated doc or still contains placeholder markers (`YYYY-MM-DD`, `[definition]`, `[What this platform is]`, bracket placeholders, or ellipsis `…` as the only content in a section).
- Report what exists, what's populated, and what still needs work.

### 2) Load the writing standards

Load the `doc-write` skill. It is the single authority on canonical document structure, tone, the schema + example + semantics pattern, precedence, versioning, and gap handling.

FBS does not ship documentation-standards docs into `docs/reference/`. That directory holds facts about the system and nothing else — the standards live in `doc-write`, loaded on demand.

### 3) Launch sub-agents to explore the codebase

Spawn the following sub-agents **in parallel** using the Agent tool. Each agent explores the codebase and writes one canonical doc with real content based on what it finds.

Sub-agents do not inherit this conversation's loaded skills. **Prefix every prompt below with:**

```
First, invoke the `doc-write` skill and follow its standards for structure, tone,
the schema + example + semantics pattern, and gap handling. Never invent a field,
endpoint, or value that isn't in the code — record gaps as `> **GAP:**` callouts.
```

#### Sub-agent A: Platform Overview

```
Prompt: "Explore this codebase to create docs/reference/PLATFORM_OVERVIEW_CANONICAL.md.

Your job:
1. Read the project's README, CLAUDE.md, package.json (or equivalent manifest), and top-level config files to understand what this project is.
2. Explore the directory structure (use Glob and Grep) to identify:
   - What the platform does and why it exists
   - Primary user types and roles
   - System boundaries (what it owns vs. integrates with)
   - High-level architecture (UI, API, workers, data stores, integrations)
   - Core domain concepts (the main entities/nouns)
   - Invariants (non-negotiable behavioral rules)
   - Multi-tenant or security model (if applicable)
3. Write the doc using declarative specification tone (state what is, not what to do).
4. If a section genuinely doesn't apply to this project, write 'Not applicable to this project.' — do NOT leave bracket placeholders.
5. If you find something ambiguous, document what you found and note the ambiguity — do NOT invent.
6. Set the frontmatter dates to today's date.

Rules: Document what exists. Raise gaps as notes, not assumptions. Use declarative tone."
```

- **subagent_type**: `general-purpose`

#### Sub-agent B: Schema & Contracts

```
Prompt: "Explore this codebase to create docs/reference/SCHEMA_AND_CONTRACTS_CANONICAL.md.

Your job:
1. Search the codebase for schema definitions, database models, API routes, type definitions, and contract structures. Check:
   - Database schemas/migrations (Prisma, Drizzle, Knex, SQLAlchemy, ActiveRecord, raw SQL, etc.)
   - API route definitions (Express, FastAPI, Next.js API routes, etc.)
   - Type/interface definitions (TypeScript interfaces, Pydantic models, Go structs, etc.)
   - Validation schemas (Zod, Yup, Joi, JSON Schema, etc.)
   - GraphQL schemas
   - OpenAPI/Swagger specs
   - Protobuf definitions
2. Document:
   - Key entity identifiers and their rules
   - Entity model with primary keys, required fields, relationships, and constraints
   - API contract conventions (request/response envelope, error model)
   - Versioning and migration policy (if evident from the codebase)
3. Use schema + example + semantics format for each operational structure you find.
4. If the project has no API or no database, state that clearly — do NOT leave placeholders.
5. Set the frontmatter dates to today's date.

Rules: Document what exists. Raise gaps as notes, not assumptions. Use declarative tone. Include real schemas from the code, not generic templates."
```

- **subagent_type**: `general-purpose`

#### Sub-agent C: Global Terminology Index

```
Prompt: "Explore this codebase to create docs/reference/GLOBAL_TERMINOLOGY_INDEX_CANONICAL.md.

Your job:
1. Read the project's README, CLAUDE.md, and key source files to identify domain-specific terminology.
2. Search for glossary sections in existing docs, comments that define terms, and domain language used consistently in the codebase.
3. Look at:
   - Model/entity names and what they represent
   - Business logic terms (e.g., 'tenant', 'workspace', 'sprint', 'pipeline')
   - Technical terms specific to this project (not general programming terms)
   - Acronyms and abbreviations used in the codebase
   - Status/state values and what they mean
4. Create a comprehensive alphabetical terminology index with clear definitions.
5. If the project has minimal domain terminology, document what exists — a short glossary is fine.
6. Set the frontmatter dates to today's date.

Rules: Document terms that actually appear in the codebase. Use declarative tone. Each definition must be specific to this project, not generic."
```

- **subagent_type**: `general-purpose`

### 4) Create the documentation inventory

After all sub-agents complete, create `docs/reference/DOCUMENTATION_INVENTORY.md`:

- List every canonical doc with its status (present and populated / present but placeholder / missing).
- Note any gaps the sub-agents identified.
- List areas that are explicitly out of scope for the current documentation installation.

### 5) Rules (apply to all docs)

- Document what exists (don't invent).
- Raise gaps as plans, not as assumptions.
- Keep canonical docs declarative (state what is, not what to do).
- Use schema + example + semantics for all operational structures.
- No bracket placeholders `[like this]` or ellipsis `…` in the final output — either document it or state it doesn't apply.
- Set all `created` and `last_updated` frontmatter dates to today's date.

## Output

1. List of docs created/confirmed with populated status
2. Any gaps identified by sub-agents
3. Recommendations for next steps (e.g., missing API docs, unclear schemas)
