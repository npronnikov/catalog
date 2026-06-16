---
name: superpowers-tdd-plan
description: >
  Generate a concrete list of test cases (Given/When/Then) before any
  implementation. Covers happy path, edge cases, and error handling.
  Produces tdd-plan.md that drives the RED-GREEN-REFACTOR cycle.
---

# TDD Plan

## Goal

Produce `tdd-plan.md` — a prioritized, numbered list of test cases that fully
specify the expected behaviour of the feature. This plan drives the RED phase:
every test in the plan must have a corresponding failing test before any
production code is written.

**Core principle:** "NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST."
This plan defines what "failing test" means for each piece of functionality.

---

## Step 1 — Understand the Feature

Load available context:
- Feature request or task description (from the node instruction)
- Answered `questions.md` if available
- Existing tests in the codebase — understand the testing style and framework

Identify:
1. What are the inputs and outputs of each public method / endpoint?
2. What invariants must always hold?
3. What error conditions must be explicitly handled?
4. What edge cases exist at boundaries (null, empty, max, min, zero)?

---

## Step 2 — Enumerate Test Cases

For each unit of behaviour, generate test cases across four categories:

### A. Happy Path
Normal inputs producing correct outputs. One test case per distinct
successful scenario.

### B. Edge Cases
Boundary values, empty collections, minimum/maximum values, single-item
lists, concurrent scenarios if relevant.

### C. Error Handling
Invalid inputs, missing required fields, type mismatches, unauthorized
access, not-found scenarios. Each error condition gets its own test case.

### D. Integration Scenarios
End-to-end flows that span multiple layers (e.g. API → service → repository).
Add only if unit tests cannot cover the interaction.

---

## Step 3 — Prioritize and Order

Order test cases so that:
1. The simplest happy path comes first (easiest to make green)
2. Edge cases follow
3. Error cases follow
4. Integration scenarios last

Each test case gets a unique ID: `TC-01`, `TC-02`, etc.

---

## Output Format

Save to `tdd-plan.md` at the path specified in the node instruction.

```markdown
# TDD Plan

## Feature
[One-sentence description of what is being implemented]

## Test Framework
[JUnit 5 / Jest / pytest / etc — detected from existing tests]

## Test Cases

### TC-01 — [Short descriptive name] [HAPPY PATH]
**Layer**: unit | integration
**Component**: [class name or endpoint]
**Priority**: high | medium | low

**Given**: [preconditions — initial state]
**When**: [action — method call or HTTP request with exact parameters]
**Then**: [expected outcome — return value, status code, side effects]

**Verify failure**: the test should fail with [specific error: NoSuchMethodError / AssertionError / 404]
before implementation exists.

---

### TC-02 — [Short descriptive name] [EDGE CASE]
...

### TC-03 — [Short descriptive name] [ERROR]
...

---

## Coverage Summary
| Category      | Count |
|---------------|-------|
| Happy path    | N     |
| Edge cases    | N     |
| Error cases   | N     |
| Integration   | N     |
| **Total**     | N     |

## Out of Scope
[List scenarios explicitly NOT being tested in this run and why]
```

---

## Constraints

- Every test case must be independently runnable.
- No implementation hints in the plan — only inputs, outputs, and assertions.
- "Verify failure" section is mandatory: it must predict the exact error the
  test will throw when no implementation exists yet.
- Test IDs must be sequential and unique.
- The plan must be self-contained — the RED phase agent must be able to write
  tests directly from this document without reading other artifacts.
