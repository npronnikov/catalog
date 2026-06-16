---
name: superpowers-tdd-red
description: >
  RED phase of TDD: write failing tests from the approved tdd-plan.md.
  No production code. Every test must fail for the right reason before
  implementation begins.
---

# TDD — RED Phase

## Goal

Write tests for every test case in `tdd-plan.md`. Run them. Confirm they all
FAIL or do not compile. Do not write a single line of production code.

**Core rule:** If a test passes before implementation, it is wrong. Fix the
test, not the implementation.

---

## Step 1 — Load the Plan

Read `tdd-plan.md` fully. Understand:
- The testing framework in use
- The exact class / method / endpoint each test targets
- The expected failure mode for each test case

Also read existing test files to understand naming conventions, base classes,
test utilities, and fixture patterns already used in the project.

---

## Step 2 — Write Tests (RED)

For each `TC-N` in `tdd-plan.md`, in order:

1. Create or open the appropriate test file.
2. Write the test method with a descriptive name matching the test case ID:
   `test_TC01_shouldReturnCreatedWhenValidInput()`
3. Write assertions based strictly on the Given/When/Then in `tdd-plan.md`.
4. Do NOT create or mock classes that don't exist yet — let the test fail to
   compile if the target class / method is not present. That is valid RED.

**Rules for test code:**
- One test method per test case ID.
- Assert the exact outcome from the `Then` clause — not a weaker assertion.
- For error cases: assert the exact exception type and message if specified.
- For HTTP endpoints: assert the exact status code and response body structure.
- Use real objects where possible; avoid mocking the system under test.

---

## Step 3 — Confirm RED

After writing all tests, run the full test suite:

```
[project test command]
```

Read the full output. For each `TC-N`:

- [ ] TC-01 — FAIL / compile error ✓
- [ ] TC-02 — FAIL / compile error ✓
- [ ] ...

**If any test passes:** the test is asserting something that already works,
or the assertion is too weak. Fix the test before proceeding.

**Expected failure modes:**
- `NoSuchMethodError` / `SymbolNotFound` — implementation class does not exist yet ✓
- `AssertionError` — implementation exists but returns wrong value ✓
- `404 Not Found` — endpoint not mapped yet ✓
- Test passes immediately — **test is wrong**, fix it

---

## Step 4 — Report

After all tests are confirmed RED, populate `step-summary`:

```
actions:
  - "wrote N test methods covering TC-01 through TC-N"
  - "ran test suite, confirmed all N tests fail"
route: "on_success"
issues: []
```

If any test could not be made to fail (and the assertion is correct because
the feature already exists):

```
route: "on_rework"
rework_instruction: |
  TC-03 passes before implementation: the endpoint POST /foo already exists
  and returns 201. This test case is redundant. Remove TC-03 from tdd-plan.md
  or replace it with a test for different behaviour.
```

---

## Constraints

- **NO PRODUCTION CODE.** Not one line of implementation.
- Do not create stub implementations to make tests compile — let them fail.
- Do not write test utilities that implicitly implement production logic.
- Do not modify existing production files.
- Only create or modify files in test source directories.
- All tests must target the exact class / endpoint from `tdd-plan.md`.
