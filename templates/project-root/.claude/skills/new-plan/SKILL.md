---
name: new-plan
description: Create a formal plan document for contract-impacting changes (schema/API/integration/business rules). Use when a change requires explicit approval before implementation.
argument-hint: "[topic]"
---

# /new-plan — Create a formal plan document

## Goal

Create a formal plan document for **contract-impacting changes** that requires explicit approval before implementation.

## When to use

Create a plan for changes to:
- **Database schemas** (tables, columns, constraints, migrations)
- **API contracts** (endpoints, request/response shapes, error envelopes)
- **Integration contracts** (external payloads, webhooks, partner APIs)
- **Business rules** (documented canonical behavior)

**Not required for:**
- Internal refactors with no contract change
- Bug fixes restoring compliance with existing specs

## Ideal workflow

```
/new-idea -> discuss architecture and design
/new-plan -> formalize design (this skill)
  pause: wait for approval
/new-sprint -> create sprint doc (agent-readable execution plan)
  execute implementation
```

## Steps

### Phase 1: Gather context

If coming from `/new-idea`, you should already have clear problem statement, proposed architecture, and key decisions. If not, ask the user to provide what contract is changing, why, and what alternatives were considered.

### Phase 2: Read the plan template

- Read `docs/plans/plan-template.md` to understand the required structure
- Optionally review recent plans in `docs/plans/` for examples

### Phase 3: Create the plan file

**Filename format**: `docs/plans/YYYY-MM-DD-<short-topic>.md`

### Phase 4: Fill each section

#### Required Metadata
- **Status**: Always start as "Draft"
- **Created**: Today's date
- **Author**: AI or human name
- **Approver**: leave blank initially
- **Related Issues**: link to relevant audit issues if applicable

#### Problem Statement
- Current State, Desired State, Why This Matters

#### Proposed Solution
- Canonical Changes Required (list affected docs, specify change type: minor/major/patch)
- Proposed Specification (schema definitions, API signatures, examples, semantics)

#### Implementation Impact
- Code changes required, new files, test changes, data/migration impact

#### Migration Plan (if breaking)

#### Alternatives Considered
- For each: description, pros/cons, why not chosen

#### Traceability
- Link to canonical docs, implementation files, test files, related audits

#### Risks
- Table: Risk | Likelihood | Impact | Mitigation

### Phase 5: Review checklist

Before presenting to user, verify:
- [ ] All required sections are filled (not just placeholders)
- [ ] Code examples are realistic (reference actual project patterns)
- [ ] Migration impact is assessed
- [ ] Risks are identified with mitigations
- [ ] Alternatives show you considered multiple approaches
- [ ] Traceability links are specific

### Phase 6: Present to user

1. Show the plan location
2. Summarize key points
3. Explain approval requirement: **stop here — do not implement until approved**
4. Ask for review

### Phase 7: Stop and wait

**CRITICAL**: Do not implement any code changes until the user explicitly approves the plan.

## After approval

1. User updates plan status to "Approved"
2. Use `/new-sprint` to create a sprint document
3. Implement sprint tasks with traceability
4. Update canonical docs after implementation
5. Mark plan as "Implemented" when complete

## Common pitfalls to avoid

- Vague problem statements ("we need to improve X")
- Missing migration plans for schema changes
- No alternatives considered
- Unrealistic risk assessments ("no risks")
- Not linking to canonical docs that need updating
