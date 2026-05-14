---
doc_type: canonical_template
status: canonical
version: 1.0.0
created: 2026-03-16
last_updated: 2026-03-16
---

# Document Template (Canonical)

This template defines the required structure for all canonical documentation in this repository. Copy this template when creating new canonical documents and fill in all sections.

---

## How to Use This Template

1. Copy this file to create a new canonical document
2. Rename using convention: `<TOPIC>_CANONICAL.md` (uppercase, underscore-separated)
3. Update frontmatter with appropriate values
4. Fill in all required sections
5. Include Schema + Example + Semantics for operational structures
6. Run pre-commit validation checklist before submission
7. Register the new document in `DOCUMENTATION_INVENTORY.md`

**Related Documents**:
- `DOCUMENTATION_STANDARDS_CANONICAL.md` — writing rules and tone requirements
- `DOCUMENTATION_HIERARCHY_CANONICAL.md` — source-of-truth precedence stack
- `DOCUMENT_MAINTENANCE_GUIDE_CANONICAL.md` — creation and editing rules

---

## Template Structure

The sections below show the required structure. Delete this "How to Use This Template" section and the "Template Structure" header when creating a real document.

---

```markdown
---
doc_type: <canonical_type>
status: draft  # Change to canonical after approval
version: 1.0.0
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
supersedes: ""  # Leave empty for new documents
---

# [Title] (Canonical)

## 0. Purpose and Scope

[Describe what this document covers]

**Purpose**:
[1-2 sentences on why this document exists]

**Scope**:
[What is included]

**Out of Scope**:
[What is explicitly excluded - refer to other documents]

**Related Documents**:
- [Reference to overview document]
- [Reference to schema document]
- [Reference to related domain document]

---

## 1. [Main Section Title]

[Declarative specification content]

### 1.1 [Subsection Title]

[Content]

### 1.2 [Subsection Title]

[Content]

---

## 2. [For Operational Structures: Include Schema + Example + Semantics]

### Schema

[TypeScript interface or JSON schema - canonical, complete, authoritative]

```typescript
interface ExampleEntity {
  id: string;              // Primary identifier
  name: string;            // Display name
  status: EntityStatus;    // Enumeration: "active" | "inactive" | "archived"
  createdAt: Date;         // Creation timestamp
  updatedAt: Date;         // Last update timestamp
}
```

### Example

[Valid JSON or code example matching schema exactly]

```json
{
  "id": "01HMFQZ9XVKQ3K2T8JXQW7YN4R",
  "name": "Example Entity",
  "status": "active",
  "createdAt": "2026-01-26T10:30:00.000Z",
  "updatedAt": "2026-01-26T10:30:00.000Z"
}
```

### Semantics

[Behavioral rules, interpretation, constraints]

- `id` is a unique identifier (format defined by the implementation)
- `name` is required and must be non-empty
- `status` transitions: [document allowed transitions here]
- `createdAt` is set at creation and never modified
- `updatedAt` is updated on every modification

---

## 3. [Additional Content Sections as Needed]

[Continue with numbered sections]

### 3.1 [Subsection]

[Content]

### 3.2 [Subsection]

[Content]

---

## N. Contract Boundaries and Document Relationships

[Define relationships with other canonical documents]

**Higher-Precedence Documents** (governs this document):
- [Reference to Documentation Standards]
- [Reference to Schema and Contracts]
- [Reference to Overview documents]

**Lower-Precedence Documents** (references this document):
- [Documents that depend on definitions here]

**Cross-References**:
- Related API endpoints: [document reference]
- Related database tables: [document reference]
- Related domain flows: [document reference]

---

## N+1. Versioning and Compatibility

**Current Version**: 1.0.0

**Version History**:

- **v1.0.0** (YYYY-MM-DD): Initial version
  - [List key contents or features]

**Compatibility Notes**:
- [Any backward/forward compatibility considerations]

**Supersession**:
- Supersedes: [Previous document if applicable]
- Superseded by: [Leave empty until superseded]

---

**End of [Document Title] v[Version]**
```

---

## Frontmatter Field Definitions

### doc_type

Choose one appropriate for the document:
- `canonical_documentation_standards`
- `canonical_documentation_hierarchy`
- `canonical_maintenance_guide`
- `canonical_template`
- `platform_overview`
- `schema_and_contracts`
- `glossary`
- `api_specification`
- `database_specification`
- `auth_specification`
- `ui_specification`
- `infrastructure_specification`
- `integration_specification`
- `domain_specification`

### status

Valid values:
- `draft` — document in progress, not yet authoritative
- `canonical` — approved, authoritative, actively maintained
- `superseded` — replaced by newer major version, kept for historical reference

### version

Format: `MAJOR.MINOR.PATCH`

- **MAJOR**: Breaking changes (field removal, behavior change)
- **MINOR**: Additive changes (new field, new endpoint, new section)
- **PATCH**: Clarifications, typo fixes, formatting (no contract change)

### created

Date document was first created (YYYY-MM-DD format).

### last_updated

Date document was last modified (YYYY-MM-DD format). Update on every change.

### supersedes

Path to previous version of document (if this is a major version update).
Leave empty for new documents.

### superseded_by

Path to newer version of document (if this document has been superseded).
Leave empty until document is superseded.

---

## Section Guidelines

### Purpose and Scope

**Required for**: all canonical documents

**Content**:
- 1-2 sentences on document purpose
- Clear list of what is covered (scope)
- Clear list of what is not covered (out of scope)
- Related documents with cross-references

**Tone**: declarative

### Schema Section

**Required for**: API endpoints, database tables, data models, configuration structures

**Content**:
- TypeScript interface or JSON schema
- Field-level comments (type, format, constraints)
- Complete structure (all fields listed)

**Tone**: declarative

### Example Section

**Required for**: all schemas

**Content**:
- Valid JSON or code example
- All required fields present
- Realistic values (not foo/bar/baz)
- Matches schema exactly

### Semantics Section

**Required for**: all schemas

**Content**:
- Field meanings and constraints
- Behavioral rules
- Lifecycle information
- Relationships to other entities
- Validation rules

**Tone**: declarative

### Contract Boundaries Section

**Required for**: all canonical documents

**Content**:
- Higher-precedence documents that govern this document
- Lower-precedence documents that reference this document
- Cross-references to related documents

**Tone**: declarative

### Versioning and Compatibility Section

**Required for**: all canonical documents

**Content**:
- Current version number
- Version history with dates and changes
- Compatibility notes
- Supersession information

**Tone**: declarative

---

## Common Documentation Patterns

### API Endpoint Pattern

```markdown
## N. POST /resource/action

### N.1 Purpose

[Declarative description of what the endpoint does]

### N.2 Request Schema

```typescript
interface RequestBody {
  field: string;    // Description
}
```

### N.3 Request Example

```json
{
  "field": "value"
}
```

### N.4 Response Schema

```typescript
interface ResponseBody {
  result: string;   // Description
}
```

### N.5 Response Example

```json
{
  "result": "value"
}
```

### N.6 Authentication

- **Required**: Yes/No

### N.7 Error Codes

| Code | HTTP Status | Meaning |
|------|-------------|---------|
| `ERROR_CODE` | 4xx | Description |

### N.8 Semantics

- [Behavioral rules grounded in the implementation]
```

### Database Table Pattern

```markdown
## N. table_name Table

### N.1 Purpose

[Declarative description of what the table stores]

### N.2 Schema

```sql
CREATE TABLE table_name (
  id TEXT PRIMARY KEY,
  field TEXT NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

### N.3 Example Row

```json
{
  "id": "01HMFQZ9XVKQ3K2T8JXQW7YN4R",
  "field": "value",
  "created_at": "2026-01-26T10:30:00.000Z"
}
```

### N.4 Constraints

**Primary Key**: `id`
**Foreign Keys**: [list]
**Indexes**: [list]

### N.5 Semantics

- [Field meanings, lifecycle, relationships]

### N.6 Lifecycle

1. **Creation**: [when/how rows are created]
2. **Active**: [normal usage]
3. **Deletion**: [when/how rows are removed]
```

---

## Pre-Commit Validation Checklist

Before submitting any canonical document, verify:

- [ ] **Frontmatter complete**: doc_type, status, version, created, last_updated
- [ ] **Purpose and Scope section** present with explicit scope definition
- [ ] **Declarative tone** throughout (no "should", "create", "add", "optional")
- [ ] **Schema + Example + Semantics** for all operational structures
- [ ] **Examples valid**: JSON/TypeScript syntax correct, matches schema exactly
- [ ] **No silent invention**: all content evidenced by codebase or approved plan
- [ ] **No cross-document duplication**: single source of truth respected
- [ ] **Cross-references valid**: all links point to existing canonical documents
- [ ] **Versioning correct**: semantic version matches change type
- [ ] **Size limit respected**: < 40,000 characters per document
- [ ] **Code blocks have language tags**: ```typescript, ```json, ```bash
- [ ] **No prohibited terms**: scan for "should", "could", "optional", "create", "add", "will"
- [ ] **Contract Boundaries section** present with cross-references
- [ ] **Versioning and Compatibility section** present with version history
- [ ] **Registered** in `DOCUMENTATION_INVENTORY.md`

---

**End of Document Template v1.0**
