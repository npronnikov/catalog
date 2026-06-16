---
name: superpowers-tdd-green
description: >
  GREEN phase of TDD: write the minimum production code to make all failing
  tests pass. No gold-plating. No features beyond what the tests require.
  All existing tests must remain green.
---

# TDD — GREEN Phase

## Goal

Make every failing test from the RED phase pass with the simplest possible
implementation. Do not write code that is not required by a failing test.

**Core rule:** "Write the simplest code necessary to pass the test."
If the simplest implementation feels wrong, that's a signal to add another
test case — not to over-engineer the current implementation.

---

## Step 1 — Load Context

Read:
- `tdd-plan.md` — understand the full intended behaviour
- The failing test files — understand exact assertions to satisfy
- The existing codebase — understand patterns, base classes, and conventions
  to follow when creating new files

Do NOT read implementation-plan.md if not provided — work from the tests.

---

## Step 2 — Implement (GREEN)

For each failing test, in priority order from `tdd-plan.md`:

1. Run the test suite to see the current failure.
2. Write the minimum production code to make this test pass:
   - Create classes / methods only as needed by the failing test.
   - Return hardcoded values if that makes the test pass — add logic only when
     a subsequent test forces it.
   - Follow existing patterns in the codebase (same package structure, same
     naming conventions, same dependency injection style).
3. Run the test suite again.
4. Confirm: this test now passes AND all previously-passing tests still pass.
5. Proceed to the next failing test.

**Layer order** (create in this sequence to minimize compile errors):
1. Domain entities / models
2. Repository interfaces
3. Service classes
4. Controllers / API handlers
5. Configuration / wiring

---

## Step 3 — No Gold-Plating

Do not:
- Add methods not required by any test
- Implement validation not tested by a test case
- Add logging, metrics, or caching not required by a test
- Create abstractions "for future extensibility"
- Refactor the code structure (that is the REFACTOR phase)

If you notice something that should be refactored, note it in a comment
`// REFACTOR: [what]` and move on. The refactor phase will address it.

---

## Step 4 — Verify Full Suite

After all tests pass:

1. Run the complete test suite (not just new tests).
2. Confirm: **zero failures, zero errors**.
3. Confirm: test count matches the expected count from `tdd-plan.md`.

---

## Step 5 — Report

Populate `step-summary`:

**All tests pass:**
```
route: "on_success"
actions:
  - "implemented [N] classes / methods"
  - "all [N] test cases now pass"
  - "full suite: [X] tests, 0 failures"
issues: []
```

**Tests still failing after implementation:**
```
route: "on_rework"
rework_instruction: |
  1. src/main/java/Foo.java — TC-03 still fails: test expects return value
     "CREATED" but method returns null. Add the missing status assignment
     at line ~42.
  2. ...
```

---

## Constraints

- Modify only production source directories (not test files).
- Every new class must follow the project's package structure and naming conventions.
- Do not introduce new dependencies without checking if the existing stack covers it.
- Do not write code that has no corresponding test case in `tdd-plan.md`.
- Verification: use only terminating build/test commands. No `bootRun`, no dev server.
