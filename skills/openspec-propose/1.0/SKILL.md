---
name: openspec-propose
description: >
  Creates the full artifact set for an OpenSpec change: proposal.md,
  delta-specs (specs/<capability>/spec.md), design.md, and tasks.md.
---

# OpenSpec Propose - Create Change Artifacts

Create an OpenSpec change with the full artifact set in one step.

**Created in** `openspec/changes/<name>/`:
- `proposal.md` - what we are doing and why
- `specs/<capability>/spec.md` - delta-specifications (documentation changes)
- `specs-index.md` - index of active delta-specs for the current change
- `design.md` - how we will implement it
- `tasks.md` - implementation checklist

---

## Inputs

The context must include:
- Change name (kebab-case, for example `add-user-auth`)
- Description of what needs to be done
- Exploration results from exploration-notes.md (if present)

---

## Steps

### 1. Create directory structure

```text
openspec/
└── changes/
    └── <name>/
        ├── proposal.md
        ├── specs-index.md
        ├── design.md
        ├── tasks.md
        └── specs/
            └── <capability>/
                └── spec.md
```

Create `openspec/changes/<name>/` and all nested directories.

### 2. Create proposal.md

**Structure:**

```markdown
# Proposal: <Change title>

## Why
[Problem or opportunity. Why it matters. What is wrong or missing today.]

## What Changes
[Concrete description of what will change. What will be added, changed, or removed.]

## Capabilities
[List of new or changed capabilities. Each capability = a separate spec file.]
- `<capability-name>`: [short description]

## Impact
[Who is affected. Risks. Dependencies. Related components.]

## Success Criteria
[Measurable criteria - how we will know the change is successful.]
```

**Rules:**
- Focus on WHAT and WHY, without implementation details
- Success criteria must be verifiable, not subjective
- Mention every capability; each will have a separate spec

### 3. Create delta-specs

For each capability from proposal.md, create:
`openspec/changes/<name>/specs/<capability>/spec.md`.

**A delta-spec describes CHANGES to the current documentation**, not a complete
rewrite.

Read the current `openspec/specs/<capability>/spec.md` (if it exists) for context.

**Delta-spec structure:**

```markdown
# <Capability> Specification Delta

## Purpose
[What this delta adds or changes relative to the current specification.]

## Requirements

### ADDED Requirements

#### Requirement: <Requirement title>
[Description of the new behavior. Focus on externally observable behavior.]

##### Scenario: <Scenario title>
- **WHEN** <condition>
- **THEN** <expected result>
- **AND** <additional condition> (optional)

### MODIFIED Requirements (if applicable)

#### Requirement: <Existing requirement title>
[Exactly what changes in the existing requirement.]

##### Scenario: <Scenario title>
...

### REMOVED Requirements (if applicable)
- `<Requirement Name>` - removal reason
```

**Spec writing rules:**
- Requirements describe observable BEHAVIOR, not implementation details
- Every requirement has at least one Scenario
- Scenario: concrete WHEN/THEN, testable and verifiable
- Implementation details (libraries, classes, methods) go to design.md, not spec

### 4. Create specs-index.md

After creating all `specs/<capability>/spec.md` files, create:
`openspec/changes/<name>/specs-index.md`.

**Purpose:** this is the single index of active delta-specs for this change used
by downstream steps.

**Structure:**

```markdown
# Specs Index: <name>

Change name: <name>
Change path: openspec/changes/<name>

## Active Delta Specs

1. Capability: `<capability-name>`
   Path: `openspec/changes/<name>/specs/<capability-name>/spec.md`
   Purpose: [what exactly this delta-spec specifies]

## Usage Contract

For implementation, verification, and archiving, use only the spec files listed
in this document as the active delta-spec set for the current change.
```

**Rules:**
- List every created spec file with no omissions
- For each spec, include capability, path, and a short purpose
- Do not add links to spec files from other changes

### 5. Create design.md

**Structure:**

```markdown
# Design: <Change title>

## Overview
[Short summary of the technical approach.]

## Decisions

### Decision: <Decision title>
**Choice:** [what was decided]
**Rationale:** [why]
**Alternatives:** [what was considered]

## Architecture
[Diagrams and component interaction schemes if applicable.]

## Data Model (if applicable)
[Changes in data model or database schema.]

## Implementation Notes
[Technical constraints, important patterns, implementation nuances.]

## Risks
[Technical risks and how to mitigate them.]
```

**Rules:**
- Put everything too detailed for spec here: technology choices, class structure, algorithms
- Record every non-trivial decision explicitly with rationale
- It may be short for small changes

### 6. Create tasks.md

**Structure:**

```markdown
# Tasks: <Change title>

## Phase 1: <Phase name>

- [ ] T001 - <Concrete action> (`path/to/file.ext`)
- [ ] T002 - <Concrete action> (`path/to/file.ext`)

## Phase 2: <Phase name>

- [ ] T003 - <Concrete action> (`path/to/file.ext`)

## Verification

- [ ] T0NN - Run tests: `<command>`
- [ ] T0NN - Check build/static validation: `<command>`
```

**Rules:**
- Every task is atomic and concrete (file, method, migration)
- Order tasks by dependencies (what must be ready earlier comes earlier)
- Phases are logically independent groups, and each phase is verifiable
- At the end, include verification through tests, build, linters, and other terminating commands
- FORBIDDEN: add verification tasks to `tasks.md` that start long-running processes
  or require "start the system and check manually":
  `npm run dev`, `vite`, `./gradlew bootRun`, `docker compose up`, background servers, watchers
- FORBIDDEN: phrase verification as:
  `start backend/frontend`, `bring up the application`, `open UI and check`,
  `perform smoke manually after starting dev servers`
- If the project has no tests, prefer checks such as `npm run build`, `./gradlew build`,
  `./gradlew compileJava`, linters, and other commands that finish with an exit code
- Add manual verification only when the specification truly requires it; do not use it as a
  substitute for required build/test checks
- Prefer repo-specific terminating commands when available:
  `cd backend && ./gradlew test`, `cd backend && ./gradlew build`, `cd backend && ./gradlew compileJava`,
  `cd frontend && npm run build`
- If you want to add a manual check, add required terminating commands first; keep the manual
  check additional and never require starting long-running processes

---

## Artifact creation rules

- Read exploration-notes.md before creation; it contains crystallized decisions
- Read current `openspec/specs/<capability>/spec.md` before writing a delta
- After creating delta-specs, collect them in `specs-index.md`
- Specs = behavior, Design = implementation, Tasks = checklist
- If context is insufficient, ask a clarifying question, but prefer reasonable decisions over questions
- After creating all files, briefly summarize what was created

---

## Final Summary

```markdown
## Created: <name>

Directory: openspec/changes/<name>/
   proposal.md      - [one line: essence of the change]
   specs-index.md   - [list of active delta-specs for the current change]
   design.md        - [one line: key technical decision]
   tasks.md         - N tasks in M phases
   specs/
       <capability>/spec.md - [one line: what is specified]

Ready for implementation. Next step: implement from tasks.md.
```
