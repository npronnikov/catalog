---
name: speckit-plan
description: >
  Creates a detailed technical implementation plan from spec.md and the project
  constitution. Covers tech stack, architecture decisions, data model, project
  structure, and constitution compliance check. Output is plan.md ready for
  task decomposition.
---

# Technical Implementation Plan

## Purpose

Translate the feature specification into a concrete technical plan that a developer
(or AI agent) can execute without guesswork. The plan makes all architecture decisions
explicit, resolves all technical unknowns, and validates compliance with the
project constitution before any code is written.

---

## How to Create the Plan

### Step 1 — Load Context

Read the following files:
- **Required**: `spec.md`
- **If exists**: `constitution.md`, `clarify-questions.md`

Then **explore the codebase**:
- Project structure: package/module organisation, existing patterns
- Existing tech stack: what frameworks and libraries are already in use
- Data layer: existing entities, migrations, repositories
- Testing approach: what kind of tests exist, how they are structured
- Conventions: naming, error handling, code organisation patterns

### Step 2 — Constitution Check

Before designing anything, list each constitution principle and evaluate:
- Does the proposed approach comply?
- Are there any tensions or conflicts?

If a violation is unavoidable, it must be explicitly justified in the plan.
An unjustified violation is a blocker — do not proceed.

### Step 3 — Research Unknowns

For each technical unknown in the spec or codebase, document:
- **Decision**: What was chosen
- **Rationale**: Why it was chosen
- **Alternatives considered**: What else was evaluated and why rejected

### Step 4 — Design the Data Model

Extract entities from the spec and clarification answers:
- Entity name, fields, types
- Relationships (one-to-many, many-to-many)
- Validation rules from requirements
- State transitions (if applicable)
- Required database migrations (if any)

### Step 5 — Define the Architecture

Describe:
- How the feature fits into the existing architecture
- Which layers are affected (domain, application, adapters, etc.)
- New modules, packages, or components to create
- Integration points with existing code
- Any new external dependencies (justify against library-first principle)

### Step 6 — Write `plan.md`

```markdown
# Implementation Plan: [Feature Name]

**Created**: YYYY-MM-DD
**Source Spec**: spec.md

---

## Constitution Check

| Principle | Status | Notes |
|-----------|--------|-------|
| Library-First | ✅ Compliant | Using existing [library] |
| Test-First | ✅ Compliant | Tests written before implementation |
| Simplicity | ✅ Compliant | No unnecessary abstractions |
| No Secrets in Code | ✅ Compliant | Secrets via env vars |

---

## Tech Stack

| Concern | Choice | Justification |
|---------|--------|---------------|
| [Concern] | [Existing or new library/framework] | [Why this, why not alternatives] |

---

## Architecture Overview

[Describe how the feature integrates into the existing architecture.
Which layers are touched. What new components are created.]

---

## Data Model

### [Entity Name]
- `field_name` (type) — [description, constraints]
- `field_name` (type, nullable) — [description]
- **Relationships**: [one-to-many with X, etc.]

### Migration
[If database migration required: describe schema changes]

---

## Project Structure

```
[List files to create or modify with Create/Modify label]
Create: src/...
Modify: src/...
```

---

## Implementation Phases

### Phase 1: [Name] (Foundation)
[What gets built first and why it must come before other phases]

### Phase 2: [Name] (Core Feature)
[Core implementation]

### Phase 3: [Name] (Integration & Tests)
[Wiring together, test coverage]

---

## Technical Decisions

### Decision 1: [Topic]
- **Chosen**: [Decision]
- **Rationale**: [Why]
- **Alternatives**: [What else was considered and why rejected]

---

## Verification Commands

```bash
# Verify each phase compiles and tests pass
[command]
```
```

---

## Key Rules

- Every technical choice must reference the existing codebase patterns — do not
  introduce new patterns without explicit justification
- If the existing stack already solves a problem, use it; do not add new dependencies
- The plan must be detailed enough that an agent can implement it mechanically,
  without needing to ask further questions
- Do not include code in the plan — describe what to build, not how it will look
- Every phase must end with a verifiable checkpoint (build passes, tests green)
