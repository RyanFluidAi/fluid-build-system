---
name: doc-audit
description: Scan the codebase against existing canonical documentation and produce a gap report. Identifies missing docs, stale docs, and docs that reference removed or changed code. Use standalone to understand documentation health.
argument-hint: "[optional: specific area to audit, e.g. 'auth', 'api', 'schema']"
---

# /doc-audit — Documentation Gap Analysis

## Goal

Produce a clear gap report comparing the codebase against existing canonical documentation in `docs/reference/`. The report drives documentation work — either immediately (by spawning doc-writers) or later (as a prioritised backlog).

## Inputs

$ARGUMENTS

If an area is specified, scope the audit to that area only. Otherwise, audit the full codebase.

## Process

### 1) Inventory existing docs

Read `docs/reference/` and list every canonical document with:
- Filename
- Subject area (what it documents)
- Last modified date (from git or frontmatter `last_updated`)

### 2) Inventory the codebase

Scan the project to understand what's implemented. Common areas to check (adapt to your stack):

| What | Where to look (examples) |
|------|---------------|
| DB schema / models | `db/`, `schema/`, `models/`, `prisma/`, `drizzle/`, migration folders |
| API routes | `api/`, `routes/`, `controllers/`, framework-specific module dirs |
| Type / contract definitions | `types/`, `*.d.ts`, validation schemas (Zod/Yup/Joi/Pydantic) |
| Event / message vocabulary | `events/`, `schemas/`, queue config |
| Auth | `auth/`, `middleware/`, session/permission modules |
| Storage / integrations | `storage/`, `integrations/`, third-party client wrappers |
| Background jobs / workers | `jobs/`, `workers/`, `queues/` |
| UI modules | `app/`, `src/`, feature directories |

Use `Glob` and `Grep` to find these. Do not assume paths — confirm them in the repo.

### 3) Compare and classify

For each codebase area, classify documentation status:

- **Missing** — code exists with no corresponding canonical doc
- **Stale** — doc exists but references types, fields, endpoints, or patterns that have changed
- **Incomplete** — doc exists but doesn't cover significant parts of the implementation
- **Current** — doc accurately reflects the implementation

### 4) Produce the gap report

Output a structured report:

```markdown
## Documentation Gap Report — YYYY-MM-DD

### Summary
- Total canonical docs: N
- Current: N
- Stale: N
- Incomplete: N
- Missing: N

### Missing documentation
| Area | Key source files | Priority | Notes |
|------|-----------------|----------|-------|

### Stale documentation
| Document | What's stale | Source of truth |
|----------|-------------|----------------|

### Incomplete documentation
| Document | What's missing | Source files to read |
|----------|---------------|---------------------|

### Recommended action order
1. ...
2. ...
```

Priority guidance:
- **P0**: Schema/API contract docs (these prevent integration bugs)
- **P1**: Event vocabulary, runtime, and core domain docs (coordination layer)
- **P2**: Module-level docs (feature understanding)
- **P3**: Utility and infrastructure docs (supporting context)

## After the report

Ask the user: "Want me to proceed with documentation updates? I'll work through each document one at a time, starting with the highest priority."

If approved, load the `/doc-write` skill and work through each document in turn, following its standards.
