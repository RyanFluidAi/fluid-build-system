---
name: doc-write
description: Standards and process for writing a single canonical document under docs/reference/. Defines formatting rules, quality requirements, AI-readability principles, and the schema+example+semantics pattern. Load before writing or updating any canonical doc.
user-invocable: false
---

# doc-write — Canonical Document Writing Standards

## Purpose

This skill is the **single authority** on how canonical documents are written, structured, versioned, and retired. Load it before creating or updating anything under `docs/reference/`.

`docs/reference/` contains facts about **this system** and nothing else. Documentation standards, templates, and maintenance rules live here, in this skill — not as canonical docs. A doc that explains how to write docs is not a fact about the system.

Canonical documentation serves two audiences equally:
1. **Human developers** — who need to understand the system quickly
2. **AI agents** — who need unambiguous, structured information to work with the system

Both audiences benefit from the same thing: precision, structure, and completeness.

---

## Source-of-truth precedence

When two documents conflict, the higher tier governs. Lower tiers never introduce, rename, or redefine anything owned by a higher tier — they cross-reference it.

1. **Schemas and contracts** — `SCHEMA_AND_CONTRACTS_CANONICAL.md`. Owns every field name, type, and constraint.
2. **Platform overview** — `PLATFORM_OVERVIEW_CANONICAL.md`. Owns architecture, boundaries, and invariants.
3. **Domain-specific canonical docs** — per-area docs you add as the system grows.
4. **Terminology** — `GLOBAL_TERMINOLOGY_INDEX_CANONICAL.md`. Owns definitions, not structure.
5. **Inventory** — `DOCUMENTATION_INVENTORY.md`. Owns nothing; it is a map.

Code outranks all of them. If canonical says X and the code does Y, that is drift — record it, don't paper over it.

---

## Document structure

Every canonical document follows this structure:

```markdown
---
doc_type: canonical
title: [Document Title]
area: [e.g., schema, api, auth, events]
status: draft | active
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
---

# [Document Title]

## Purpose and scope

[1-2 sentences: what this document covers and what it does NOT cover]

## [Content sections — vary by subject]

## Cross-references

- [Links to related canonical docs]
```

---

## Writing principles

### 1. Accuracy above all

Every statement must be verifiable in the source code. Before writing any claim:
- Read the relevant source file
- Check the actual type definition, not what you think it is
- Verify default values, constraints, and nullability in the schema

**Bad:** "Work items have a status field"
**Good:** "The `state` field is a `text` column, NOT NULL, defaulting to `\"open\"`. Accepted values: `open`, `in_progress`, `blocked`, `done`."

### 2. Completeness over brevity

Document everything in the area. A gap in documentation is worse than a verbose document. For every module, document:
- Every table and its columns
- Every API endpoint and its request/response shapes
- Every event type and its payload
- Every exported function that forms part of the public API
- Every enum and its values
- Every relationship between entities

### 3. Precision of language

Use exact, unambiguous language. AI agents cannot infer intent from vague descriptions.

| Vague (don't use) | Precise (use this) |
|---|---|
| "Items can be assigned to users" | "The `assigneeId` field on `item` is a nullable `text` column that references `user.id`" |
| "Events are emitted when things change" | "The `item.created` event is emitted inside `mutate()` when `createItem()` inserts a new row" |
| "The API returns the item" | "The `POST /api/items` endpoint returns `201` with the full `Item` object including server-generated fields (`id`, `createdAt`)" |
| "Priority is optional" | "The `priority` field is an `integer` column, nullable, defaulting to `3`. Range: 1 (critical) to 5 (low)." |

### 4. Structure for scanning

Documents are reference material — looked up, not read cover-to-cover. Every section must be independently useful:
- Use tables for field definitions (not prose lists)
- Use code blocks for types and examples (not inline descriptions)
- Use headings that match the thing being documented (`### POST /api/items`, not `### Creating items`)
- Group related items (all endpoints together, all events together)

### 5. No filler

Every sentence must carry information. Remove:
- Introductory phrases ("It's worth noting that...", "As mentioned above...")
- Obvious statements ("This section describes the schema")
- Hedging language ("This might be useful when...", "Consider using...")
- Meta-commentary ("The following table shows...", "See below for details")

Just state the facts.

---

## The Schema + Example + Semantics pattern

For every operational data structure, use this three-part format. This is mandatory — no exceptions.

### Part 1: Schema

Show the actual type or table definition as it appears in code. Example (Drizzle/Postgres):

```typescript
export const item = pgTable("item", {
  id: text("id").primaryKey(),
  title: text("title").notNull(),
  priority: integer("priority").default(3),
  state: text("state").notNull().default("open"),
  organizationId: text("organization_id").notNull().references(() => organization.id),
  assigneeId: text("assignee_id").references(() => user.id),
  createdAt: timestamp("created_at").defaultNow().notNull(),
});
```

Adapt the language to your stack (Prisma, TypeORM, SQLAlchemy, Pydantic, Go structs, etc.) — show what's actually in the codebase.

### Part 2: Example

Show a concrete, realistic JSON instance. Use realistic values, not placeholders like "string" or "123":

```json
{
  "id": "01J5A3B7C8D9E0F1G2H3I4J5K6",
  "title": "Follow up on Q3 proposal",
  "priority": 2,
  "state": "in_progress",
  "organizationId": "01J5A3B7C8D9E0F1G2H3I4J5K7",
  "assigneeId": "01J5A3B7C8D9E0F1G2H3I4J5K8",
  "createdAt": "2026-04-10T14:30:00.000Z"
}
```

### Part 3: Semantics

A table explaining every field. Every row must include type, whether it's required, and a description that covers constraints, defaults, and relationships:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | `text` (UUIDv7) | Yes | Primary key. |
| `title` | `text` | Yes | Human-readable description. No length limit enforced. |
| `priority` | `integer` | No | 1 = critical, 5 = low. Defaults to `3`. |
| `state` | `text` | Yes | One of: `open`, `in_progress`, `blocked`, `done`. Defaults to `"open"`. |
| `organizationId` | `text` (UUIDv7) | Yes | FK to `organization.id`. Scopes the item to an org. |
| `assigneeId` | `text` (UUIDv7) | No | FK to `user.id`. Nullable — unassigned items have `null`. |
| `createdAt` | `timestamp` (ISO 8601) | Yes | Auto-set on insert. Never updated. |

---

## API endpoint documentation format

For each endpoint:

```markdown
### POST /api/items

Creates a new item within the authenticated user's organization.

**Authentication:** Required (session cookie or API key)

**Request body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | `string` | Yes | The item title |
| `priority` | `integer` | No | 1-5, defaults to 3 |
| `assigneeId` | `string` | No | UUIDv7 of the user to assign |

**Response `201`:**

Returns the created `Item` object (see schema above).

**Errors:**

| Status | Code | When |
|--------|------|------|
| `400` | `VALIDATION_ERROR` | Missing required fields or invalid values |
| `401` | `UNAUTHORIZED` | No valid session |
| `403` | `FORBIDDEN` | User lacks permission in this organization |
```

---

## Event documentation format

For each event:

```markdown
### item.created

Emitted when a new item is inserted.

**Payload:**

| Field | Type | Description |
|-------|------|-------------|
| `itemId` | `string` (UUIDv7) | The created item's ID |
| `title` | `string` | The item title |
| `organizationId` | `string` (UUIDv7) | The owning organization |

**Emitted by:** `createItem()` in `<path/to/source>`

**Subscribers:**
- `activity-logger` — writes activity log entry
- `search-indexer` — updates search index
```

---

## Tone rules

### Use declarative specification tone

State what IS, not what to DO.

**Bad:** "You should set the priority field to a value between 1 and 5"
**Good:** "The `priority` field accepts integer values 1-5."

**Bad:** "Create a new item by calling the POST endpoint"
**Good:** "The `POST /api/items` endpoint creates an item."

### Prohibited words and phrases

Never use: should, could, optional (use "nullable" or "not required"), might, consider, try, will, would, can, may, please, note that, it is important to, remember to, keep in mind, as mentioned, see below, the following.

---

## No silent invention

The hardest rule, and the one most often broken.

**Never introduce a field, endpoint, event, status value, or behaviour that does not exist in the code.** Not as an example, not as an "obvious" completion of a pattern, not as a placeholder. A canonical doc is read as authoritative; an invented field becomes real the moment someone implements against it.

When you find a gap:

| Gap type | What to do |
|----------|-----------|
| **Blocking** — the structure can't be documented without the missing piece (undefined enum, unclear FK target, ambiguous nullability) | Stop. Record the gap explicitly in the doc as a `> **GAP:**` callout naming the file you checked and what you couldn't determine. Do not guess. |
| **Non-blocking** — a nice-to-have detail is absent (no description comment, unclear intent behind a default) | Document what exists, omit what doesn't, and move on. |
| **Needs a decision** — the code is inconsistent, or the right contract is genuinely undecided | This is a plan, not a doc edit. Note it and hand it to `/new-plan`. |

Every claim must trace to a file you actually read. "I checked `src/db/schema.ts:42`" is the standard, not "this is how these usually work."

---

## Lifecycle and versioning

### Statuses

| Status | Meaning |
|--------|---------|
| `draft` | Being written. Not yet authoritative. |
| `active` | Authoritative. This is the source of truth. |
| `superseded` | Replaced. Kept for history; `superseded_by` points to the replacement. |

### Change types

Classify every canonical change, and record it in the plan when one exists:

| Type | Meaning | Example |
|------|---------|---------|
| **Patch** | Clarification only. No contract change. | Fixing a typo in a description, adding a missing constraint that was always enforced. |
| **Minor** | Additive. Existing consumers keep working. | New optional field, new endpoint, new enum value. |
| **Major** | Breaking. Existing consumers break. | Removed or renamed field, changed type, narrowed enum, changed error envelope. |

A **Major** change to a canonical doc requires an approved plan before the doc is edited — the doc follows the code, and the code needed a plan.

### Supersession

Replace a document rather than gutting it when its scope fundamentally changes:

1. Create the new document.
2. Set `superseded_by:` in the old document's frontmatter and `status: superseded`.
3. Set `supersedes:` in the new document.
4. Update every cross-reference that pointed at the old one.
5. Update `DOCUMENTATION_INVENTORY.md`.

Never delete a superseded canonical doc. Git history is not a substitute for an explicit pointer.

---

## Cross-references

- Link by **path and heading**: `See SCHEMA_AND_CONTRACTS_CANONICAL.md#item-table`.
- Reference definitions; never restate them. If the same field table appears in two docs, one of them is wrong and both will drift.
- When you split a document, add cross-references in both directions.

---

## Rules for updates vs creation

### When updating an existing document

- **Minimal diffs** — change only what needs changing
- **Preserve structure** — don't reorganise sections that work fine
- **Preserve correct content** — don't rewrite accurate paragraphs
- **Add rows** — when adding a field, add a row to the existing table
- **Never restructure without being asked** — a reorganisation buries the actual change in the diff
- **Update frontmatter** — set `last_updated` to today's date

### When creating a new document

- **Filename**: uppercase with `_CANONICAL` suffix to match existing convention: `SCHEMA_AND_CONTRACTS_CANONICAL.md`, `EVENT_VOCABULARY_CANONICAL.md`
- **Read all source files first** — understand the full implementation before writing
- **Purpose and Scope first** — make boundaries immediately clear
- **Complete coverage** — document every type, endpoint, event, and table
- **Cross-reference** — link to related canonical docs

---

## Size limits

- **Maximum**: 40,000 characters per document
- If larger, split by entity (e.g., `CRM_LEADS_CANONICAL.md`, `CRM_COMPANIES_CANONICAL.md`)
- **Minimum**: frontmatter + Purpose and Scope + at least one Schema+Example+Semantics block + Cross-references

---

## What NOT to document

- Internal helper functions and private utilities (they change frequently)
- Aspirational features not yet in code
- Workarounds or temporary hacks (these go in `docs/solutions/`)
- Content already in another canonical doc (reference it instead)
- Implementation notes ("we chose X because Y") — that belongs in the sprint doc's Notes section

---

## Self-review checklist (run before finishing)

- [ ] Every claim traces to a file you actually read
- [ ] Nothing invented — no field, endpoint, or value that isn't in the code
- [ ] Gaps recorded as explicit `> **GAP:**` callouts, not guessed at
- [ ] No prohibited words or hedging language
- [ ] Every operational structure has Schema + Example + Semantics
- [ ] Examples use realistic values, not placeholders
- [ ] No bracket placeholders `[like this]` or ellipsis `…` as the only content of a section
- [ ] Nothing restated that another canonical doc owns — cross-referenced instead
- [ ] Change classified as Patch / Minor / Major; Major has an approved plan
- [ ] `last_updated` (and `created` if new) frontmatter is today's date
- [ ] Cross-references to related canonical docs are present
