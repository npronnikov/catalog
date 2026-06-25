---
name: superpowers-tdd-refactor
description: >
  REFACTOR phase of TDD: improve code quality while keeping all tests green.
  No new behaviour. Uses systematic debugging if a refactor breaks tests.
  Produces clean, idiomatic code ready for human review.
---

# TDD — REFACTOR Phase

## Goal

Improve the production code written in the GREEN phase without changing any
observable behaviour. All tests must remain green after every change.

**Core rule:** If a refactoring causes a test to fail, the refactoring
introduced a regression — restore the last known green state, understand why,
then try again. Tests are the safety net; a broken test means a broken
refactoring.

---

## Step 1 — Identify Refactoring Targets

Scan the code written in the GREEN phase for:

| Target | Examples |
|--------|---------|
| Duplication | Same logic in two methods / classes |
| Poor names | `data`, `temp`, `result`, `x` without context |
| Long methods | Methods doing more than one thing |
| Magic values | Hardcoded strings / numbers with no explanation |
| Unnecessary complexity | Nested ternaries, deep nesting (> 3 levels) |
| Wrong abstraction level | Business logic in controller, I/O in domain |
| Missing null checks | Only if a test exercises the null path |
| `// REFACTOR:` comments | Left during GREEN phase |

Do NOT chase perfection. Target only changes that make the code clearly
better and reduce future maintenance cost. Three similar lines are better
than a premature abstraction.

---

## Step 2 — Refactor Incrementally

For each refactoring target:

1. **Make one change** — rename one variable, extract one method, move one class.
2. **Run the full test suite immediately.**
3. If all tests pass — proceed to the next target.
4. If any test fails — **stop and apply systematic debugging:**

   **Systematic debugging (4 phases):**
   - Phase 1: Read the failure message precisely. What assertion failed?
   - Phase 2: Identify the last working state (before this refactor step).
   - Phase 3: Form a hypothesis: which change caused this failure?
   - Phase 4: Restore the last known green state (by revert or immediate fix),
     verify tests go green, then re-apply more carefully.

   If three attempts to fix the refactoring fail, abandon that refactoring goal
   and skip it.
   Leave a `// REFACTOR-DEFERRED:` comment explaining what was attempted.

---

## Step 3 — Specific Refactoring Rules

### Extract Method
- Only if the extracted block has a single, clear responsibility.
- Name must describe what the method does, not how.
- After extraction: run tests.

### Rename
- Names must match project conventions (check existing code for style).
- After rename: run tests (rename may miss usages).

### Remove Duplication
- Extract shared logic only when it appears in 3+ places with identical intent.
- After extraction: run tests and verify all call sites still behave correctly.

### Reorder / Reorganize
- Moving code between packages or classes: update all imports.
- After move: full build + tests.

---

## Step 4 — Final Quality Check

After all refactoring is complete:

1. Run the full test suite. **Must be: zero failures, zero errors.**
2. Run the linter / format checker if available.
3. Scan for remaining `// REFACTOR:` comments — handle or convert to `// REFACTOR-DEFERRED:`.
4. Verify no new public API was accidentally introduced or removed.
5. Verify no `// TODO` or debug output was left in production code.

---

## Step 5 — Report

Populate `step-summary`:

**Refactoring complete, all tests green:**
```
route: "on_success"
actions:
  - "extracted [N] methods"
  - "renamed [M] variables/classes for clarity"
  - "removed duplication in [location]"
  - "full suite: [X] tests, 0 failures"
issues: []
```

**Refactoring deferred (some targets were too risky):**
```
route: "on_success"
actions: [...]
issues:
  - severity: "minor"
    description: "REFACTOR-DEFERRED: FooService.process() could be split but
      three attempts caused test failures — left as-is for safety"
```

**Refactoring broke tests and cannot be resolved:**
```
route: "on_rework"
rework_instruction: |
  Refactoring of BarRepository caused TC-05 to fail (null pointer in save()).
  The rename from 'repo' to 'repository' missed the constructor injection at
  BarService.java:23. Fix the injection before continuing.
```

---

## Constraints

- Do NOT add new functionality during refactoring.
- Do NOT change test code to make refactoring easier — tests are the spec.
- Run tests after **every single change** — not batched at the end.
- Restore a green baseline before continuing after any refactoring-induced test failure.
- Use only terminating build/test commands for verification.
