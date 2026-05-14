---
doc_type: canonical_maintenance_guide
status: canonical
version: 1.0.0
created: 2026-03-16
last_updated: 2026-03-16
---

# Document Maintenance Guide (Canonical)

## 0. Purpose and Scope

This document defines the authoritative rules for creating, editing, and updating canonical documentation in this repository. It applies to all AI agents and human editors producing or modifying documentation in `docs/reference/` and ensures alignment with the Documentation Standards and Documentation Hierarchy.

**Related Documents**:
- `DOCUMENTATION_STANDARDS_CANONICAL.md` — writing rules and tone requirements
- `DOCUMENTATION_HIERARCHY_CANONICAL.md` — source-of-truth precedence stack
- `DOCUMENT_TEMPLATE_CANONICAL.md` — template for new canonical documents

---

## 1. Document Creation Rules

### 1.1 When to Create a New Document

Create a new canonical document when:
- A new domain or sub-domain requires authoritative specification
- An existing document exceeds 40,000 characters and must be split
- A major version increment requires a new document (supersession)

Do not create a new document when:
- Content belongs in an existing document
- The change is a minor or patch version update
- The content is operational (use `docs/sprints/`, `docs/audits/`, `docs/plans/`)

### 1.2 Document Creation Workflow

1. **Identify correct family** using `DOCUMENTATION_HIERARCHY_CANONICAL.md`
2. **Choose appropriate name** (uppercase, underscore-separated, `_CANONICAL.md` suffix)
3. **Copy document template** from `DOCUMENT_TEMPLATE_CANONICAL.md`
4. **Fill frontmatter** (doc_type, status, version, dates)
5. **Draft content** following schema + example + semantics pattern
6. **Validate** against the pre-commit checklist (Section 5)
7. **Register** the new document in `DOCUMENTATION_INVENTORY.md`

### 1.3 Frontmatter Requirements

All new documents must include:

```yaml
---
doc_type: <canonical_type>
status: draft  # Start as draft, promote to canonical after review
version: 1.0.0
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
supersedes: ""  # Empty for new documents
---
```

### 1.4 Required Sections

All new canonical documents must include:

```markdown
# [Title] (Canonical)

## 0. Purpose and Scope
[What this document covers and what it excludes]

## 1-N. [Content Sections]
[Schema + Example + Semantics for operational structures]

## N. Contract Boundaries and Document Relationships
[Cross-references to related canonical documents]

## N+1. Versioning and Compatibility
[Version history and compatibility notes]
```

### 1.5 Fit Within Hierarchy

New documents must:
- Fit within the precedence stack defined by the documentation hierarchy
- Not duplicate content from other canonical documents
- Reference higher-precedence documents where applicable
- Follow the source-of-truth precedence stack

---

## 2. Document Editing and Update Rules

### 2.1 Minor and Patch Updates

**Minor updates** (new content, additive changes):
1. Update the targeted section only
2. Increment minor version in frontmatter: `version: 1.1.0`
3. Update `last_updated` date
4. Do not change filename
5. Run compliance check

**Patch updates** (clarifications, typo fixes):
1. Make minimal targeted change
2. Increment patch version in frontmatter: `version: 1.0.1`
3. Update `last_updated` date
4. Do not change filename
5. Run compliance check

### 2.2 Major Updates (Breaking Changes)

**Major updates** (field removal, behavior change, incompatible schema):
1. Create new document with `_V2` or similar suffix if needed
2. Mark old document as superseded (see Section 4)
3. Migrate all cross-references
4. Run full validation

### 2.3 Edits Modify Only Requested Sections

Edits must:
- Modify only the requested sections
- Preserve existing wording unless explicitly instructed to change
- Not restructure unrelated sections
- Maintain all metadata requirements (tone, structure, naming, canonical ordering)

### 2.4 No New Fields Without Approval

No new fields, endpoints, tables, or structures may be introduced unless:
- Explicitly approved by the user
- Evidence exists in the codebase
- A plan exists and is approved (for contract-impacting changes)

---

## 3. AI Agent Editing Rules

### 3.1 Targeted, Minimal Replacements

AI agents use targeted replacements:
- Identify exact text to replace (sufficient context for uniqueness)
- Provide replacement text only (no explanations inside the document)
- Preserve formatting, indentation, and structure
- Verify replacement success

### 3.2 No Commentary Inside Documents

AI agents must not add:
- Meta explanations ("This section describes...")
- Reasoning ("We chose this because...")
- Conversational asides ("Note that...")
- Implementation guidance ("To use this, you should...")

### 3.3 Preserve Formatting and Structure

Edits must preserve:
- YAML frontmatter formatting
- Markdown heading levels
- Code fence languages (```typescript, ```json, ```bash)
- List indentation (2 spaces)
- Line breaks and spacing
- Link formats

### 3.4 Explicit Confirmation Before Editing

Before making changes, AI agents confirm:
- Document path being edited
- Section being modified
- Nature of change (addition, update, deletion)
- Impact on version number (major, minor, patch)

---

## 4. Versioning and Supersession Rules

### 4.1 Semantic Versioning

Documents use semantic versioning (MAJOR.MINOR.PATCH):

- **MAJOR** (breaking change):
  - Field removal or rename
  - Behavior change that breaks existing integrations
  - Schema incompatibility
  - Requires new document file

- **MINOR** (additive change):
  - New field addition
  - New endpoint or table
  - New section or subsection
  - Backward compatible
  - Updates frontmatter only

- **PATCH** (clarification):
  - Typo fix
  - Formatting improvement
  - Clarification without behavior change
  - Example correction
  - Updates frontmatter only

### 4.2 Supersession Workflow

When creating a major version (new file):

**Old document**:
```yaml
---
doc_type: <type>
status: superseded
version: 1.5.3
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
superseded_by: "<NEW_DOCUMENT_FILENAME>.md"
---
```

**New document**:
```yaml
---
doc_type: <type>
status: canonical
version: 2.0.0
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
supersedes: "<OLD_DOCUMENT_FILENAME>.md"
---
```

### 4.3 Migration Path

After creating a new major version:
1. Update all cross-references to point to new document
2. Add migration notes in new document's "Versioning and Compatibility" section
3. Keep old document for historical reference (do not delete)

---

## 5. Validation Requirements

### 5.1 Pre-Commit Checklist

Before marking a document as canonical, validate:

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

### 5.2 Compliance Check Examples

**Declarative tone check**:
- No: "You should use this endpoint to authenticate."
- Yes: "The endpoint authenticates users."

**Schema + example + semantics check**:
```markdown
### Schema
[TypeScript interface or JSON schema]

### Example
[Valid JSON matching schema exactly]

### Semantics
[Behavioral rules and constraints]
```

**Cross-reference check**:
- No: redefining a schema from a higher-precedence document in a lower-precedence one
- Yes: referencing the higher-precedence document: "See `SCHEMA_AND_CONTRACTS_CANONICAL.md`"

---

## 6. Schema, Example, and Semantics Requirements

### 6.1 When Schema + Example + Semantics is Required

Required for:
- API request/response bodies
- Database table definitions
- Authentication flows with state
- Configuration structures
- Data models and entities
- Error envelopes

Not required for:
- Conceptual overview sections
- Navigation descriptions
- High-level architecture diagrams

### 6.2 Schema Format

**TypeScript interface** (preferred):
```typescript
interface ExampleEntity {
  id: string;              // Primary identifier
  name: string;            // Display name
  status: EntityStatus;    // Enumeration: "active" | "inactive" | "archived"
  createdAt: Date;         // Creation timestamp
  updatedAt: Date;         // Last update timestamp
}
```

**JSON schema** (when TypeScript not applicable):
```json
{
  "type": "object",
  "properties": {
    "id": {"type": "string", "format": "uuid"},
    "name": {"type": "string"},
    "status": {"type": "string", "enum": ["active", "inactive", "archived"]},
    "createdAt": {"type": "string", "format": "date-time"},
    "updatedAt": {"type": "string", "format": "date-time"}
  },
  "required": ["id", "name", "status", "createdAt", "updatedAt"]
}
```

### 6.3 Example Format

Examples must:
- Be valid JSON or code
- Match the schema exactly
- Include all required fields
- Use realistic values (not foo/bar/baz)
- Demonstrate the structure's purpose

### 6.4 Semantics Format

Semantics describe:
- Field meanings and constraints
- Lifecycle and state transitions
- Relationships to other entities
- Validation rules
- Business logic rules

---

## 7. Cross-Document Reference Rules

### 7.1 Reference Format

Use consistent format for cross-references:

```markdown
See: `<DOCUMENT_NAME_CANONICAL>.md`

Examples:
- See schemas: `SCHEMA_AND_CONTRACTS_CANONICAL.md`
- See terminology: `GLOBAL_TERMINOLOGY_INDEX_CANONICAL.md`
```

### 7.2 Section References

Reference specific sections when appropriate:

```markdown
See: `<DOCUMENT_NAME_CANONICAL>.md#<section-number>-<section-slug>`
```

### 7.3 Do Not Duplicate Canonical Definitions

References must not:
- Redefine schemas from higher-precedence documents
- Duplicate behavioral rules from higher-precedence documents
- Introduce new fields not present in canonical schemas

Instead:
- Link to the authoritative source
- Quote or summarize key points
- Provide additional context specific to the current document's scope

---

## 8. Document Splitting Rules

### 8.1 When to Split a Document

Split a document when:
- It exceeds 40,000 characters
- A section exceeds 4,000 characters (except schemas)
- The document covers multiple unrelated sub-domains
- The document becomes difficult to navigate

### 8.2 Splitting Strategy

Split along hierarchy-defined boundaries. Each resulting document:
- Gets its own name and frontmatter
- Covers a coherent sub-domain
- Cross-references sibling documents

### 8.3 Cross-References After Splitting

After splitting, add a "See Also" section to each resulting document:

```markdown
## See Also

- Overview: `<OVERVIEW_CANONICAL>.md`
- Sub-domain A: `<SUBDOMAIN_A_CANONICAL>.md`
- Sub-domain B: `<SUBDOMAIN_B_CANONICAL>.md`
```

---

## 9. Tone and Style Rules

### 9.1 Declarative Specification Tone

Describe the system as it exists:
- Yes: "The system validates email format."
- No: "The system should validate email format."

### 9.2 Prohibited Terms

Avoid:
- Instructional: create, add, remove, update, configure, set up
- Conditional: should, could, would, might, can
- Temporal: will, going to, eventually
- Optional: optional (use "nullable" or "omittable" with precise semantics)

### 9.3 Permitted Replacements

- "The field is nullable" (not "The field is optional")
- "The system validates" (not "You should validate")
- "The endpoint accepts" (not "You can send")
- "The flow proceeds" (not "The flow will proceed")

### 9.4 Punctuation

- Use hyphen (-) not en dash or em dash
- Use standard ASCII quotes (" and ') not curly quotes
- Avoid exclamation marks and question marks in specification text

---

## 10. Error Handling and Gap Management

### 10.1 Blocking Gaps

**Surface immediately** when:
- Missing required field prevents documenting an existing endpoint
- Contradictory information between codebase and specification
- Undefined behavior blocks schema definition

**Response workflow**:
1. Pause writing task
2. Notify user of gap with context
3. Propose solution with rationale
4. Wait for explicit approval
5. Continue after approval

### 10.2 Non-Blocking Gaps

**Defer to review** when:
- Potential optimization suggestion
- Additional example would improve clarity
- Clarification does not block documentation

**Handling**:
- Note gap for improvement review
- Continue drafting current document
- Present gap list at completion review

### 10.3 Evidence Requirements

All content must be:
- Evidenced by codebase (schemas, API routes, database tables)
- Approved via plan (for new contract-impacting features)
- Derived from existing canonical documents (higher-precedence)

Do not invent:
- Fields not present in codebase or approved plans
- Endpoints not implemented
- Tables not defined in database schema
- Behaviors not implemented in code

---

## 11. Workflow Efficiency Rules

### 11.1 Document Creation Mode

During active drafting:
- Minimize interruptions for non-blocking gaps
- Focus on completing current document
- Do not trigger cross-document updates
- Save improvement suggestions for review phase

### 11.2 Incremental Drafting

Draft large documents incrementally:

1. **Skeleton**: Create document with frontmatter, Purpose and Scope, main section headings
2. **Core content**: Draft schemas and primary behavioral sections
3. **Examples**: Add valid examples matching schemas
4. **Semantics**: Document behavioral rules and constraints
5. **Cross-references**: Add links to related documents
6. **Review**: Run compliance check and validate

### 11.3 Review Checkpoints

Request review at:
- Completion of major section
- Before proceeding to next document in a series
- When approaching 40,000 character limit
- After addressing blocking gaps

---

## 12. Lifecycle and Status Management

### 12.1 Document Statuses

- **draft**: Document in progress, not yet canonical
- **canonical**: Approved, authoritative, actively maintained
- **superseded**: Replaced by newer major version, kept for historical reference

### 12.2 Status Transitions

**Draft to Canonical**:
1. Complete all required sections
2. Run pre-commit validation checklist
3. User approval
4. Update frontmatter: `status: canonical`

**Canonical to Superseded**:
1. Create new major version
2. Mark old document: `status: superseded`
3. Add `superseded_by` to old document frontmatter
4. Add `supersedes` to new document frontmatter
5. Migrate cross-references

### 12.3 Maintenance Cadence

Canonical documents are reviewed:
- When underlying implementation changes
- When plans are approved and implemented
- During periodic audits (see `docs/audits/`)
- When issues are identified (cross-document drift, stale examples)

---

## 13. Plan Integration

### 13.1 Plan-Driven Changes

Contract-impacting changes require plans:

1. Create plan in `docs/plans/`
2. Wait for approval
3. Update canonical schemas (if schema change)
4. Update affected domain documents (if behavior change)
5. Run validation and compliance checks

### 13.2 Traceability

Link plans to canonical documents:

**In plan**:
```markdown
**Canonical documents updated**:
- `SCHEMA_AND_CONTRACTS_CANONICAL.md` (added new field)
- `PLATFORM_OVERVIEW_CANONICAL.md` (updated architecture section)
```

**In canonical document**:
```markdown
## Versioning and Compatibility

**v1.1.0** (YYYY-MM-DD):
- Added `newField` (see plan: `docs/plans/PLAN-NNN-YYYY-MM-DD-topic.md`)
- Backward compatible: field defaults to null for existing records
```

---

## 14. Common Maintenance Scenarios

### 14.1 Adding a New API Endpoint

1. Check if a relevant canonical document exists
2. If yes: add endpoint documentation (minor version bump)
3. If no: create new canonical document using the template
4. Ensure input/output schemas reference `SCHEMA_AND_CONTRACTS_CANONICAL.md`
5. Document authentication, authorization, errors
6. Provide request/response examples
7. Update cross-references

### 14.2 Adding a New Database Table

1. Update or create the relevant canonical document
2. Add schema reference to `SCHEMA_AND_CONTRACTS_CANONICAL.md`
3. Document all columns, indexes, constraints, foreign keys
4. Provide example row
5. Document semantics (lifecycle, relationships)

### 14.3 Changing a Schema Field (Breaking)

1. Create plan for schema change
2. Wait for approval
3. Create new major version of the schema document
4. Update all documents that reference the schema
5. Document migration path in "Versioning and Compatibility" section

### 14.4 Clarifying Existing Documentation

1. Identify section needing clarification
2. Make targeted edit (patch version bump)
3. Update `last_updated` date
4. Ensure no behavioral changes introduced
5. Run compliance check

---

**End of Document Maintenance Guide v1.0**
