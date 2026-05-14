---
name: documentation-governance
description: High-level governance rules for canonical documentation under docs/reference/. Defers writing rules to /doc-write and workflow to /doc-sprint-sync. Load when you need a reminder of non-negotiables (no silent invention, plan-approval gate for contract changes).
user-invocable: false
---

# Documentation Governance

This is the short reference for canonical-doc governance. Detailed standards and workflow live in dedicated skills.

## Where things live

| Concern | Skill / file |
|---------|-------------|
| Writing standards (tone, schema+example+semantics, prohibited words) | `/doc-write` skill |
| Post-sprint documentation sync workflow | `/doc-sprint-sync` skill |
| Gap analysis / drift detection | `/doc-audit` skill |
| Initial one-time setup of canonical docs | `/install-documentation` skill |
| Canonical-doc precedence and scope boundaries | `docs/reference/DOCUMENTATION_HIERARCHY_CANONICAL.md` |
| Full writing specification | `docs/reference/DOCUMENTATION_STANDARDS_CANONICAL.md` |
| Creation, editing, versioning, supersession rules | `docs/reference/DOCUMENT_MAINTENANCE_GUIDE_CANONICAL.md` |
| Copy/paste template for new canonical docs | `docs/reference/DOCUMENT_TEMPLATE_CANONICAL.md` |

## Non-negotiables

- Do not introduce new fields, enums, or behaviors silently.
- Do not duplicate or redefine schemas across multiple documents.
- When a change is contract-impacting, create a plan in `docs/plans/` and wait for approval before updating canonical docs.
- Doc updates are **not** a sprint-close gate. They are a separate, owner-triggered workflow (`/doc-sprint-sync`).

## Common pitfalls

- **Pitfall**: Writing instructions ("should", "recommended") into canonical docs.
  - **Fix**: Move guidance into skills; keep canonical docs declarative.
- **Pitfall**: Adding an example field not present in schema.
  - **Fix**: Raise a gap and require explicit approval before adding any new field.
- **Pitfall**: Blocking sprint close on documentation updates.
  - **Fix**: Close the sprint via `/review-sprint`. Run `/doc-sprint-sync` afterward when the owner decides docs need refreshing.
