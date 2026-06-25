---
name: superpowers-test-driven-development
description: >
  Apply focused Test-Driven Development while implementing a feature or bugfix.
  Work one behavior at a time: write a failing test, watch it fail for the
  right reason, write minimal code to pass, then refactor while staying green.
---

# Test-Driven Development

## Goal

Apply TDD at the behavior level during implementation. Do not batch the entire
feature into one global RED phase and one global GREEN phase.

**Core principle:** NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST.

---

## Step 1 — Pick One Behavior

From the active implementation plan, take the next smallest behavior that can
be expressed as one focused test.

Use one local cycle per behavior:
1. Write the failing test
2. Run it and verify RED
3. Write the minimum code to pass
4. Run it and verify GREEN
5. Refactor lightly while staying green

Then move to the next behavior.

---

## Step 2 — Verify RED

Run the narrowest terminating test command that proves the new test fails for
the expected reason.

Confirm:
- The new test fails before implementation
- The failure is caused by missing or incorrect behavior, not by a typo
- Existing tests are not reinterpreted as "must fail"

If the test already passes before any new production change:
- first tighten the test if it is too weak
- if the behavior truly already exists, raise it as a planning / scope issue

---

## Step 3 — Implement GREEN

Write the simplest production change that makes the current failing test pass.

Rules:
- No extra features
- No speculative abstractions
- No refactoring unrelated code during GREEN
- Keep the implementation scoped to the current behavior

After the change:
- rerun the focused test
- confirm the new test passes
- confirm previously green tests remain green

---

## Step 4 — Small REFACTOR

Only after GREEN:
- improve names
- remove obvious duplication
- extract tiny helpers if they clarify the code

After each refactor step:
- rerun the relevant tests
- restore green state immediately if the refactor breaks behavior

---

## Step 5 — Repeat

Continue behavior by behavior until all plan steps are complete.

The expected cadence is:
- RED for one behavior
- GREEN for that same behavior
- small REFACTOR
- next behavior

Not:
- all RED for the feature
- all GREEN for the feature
- one giant REFACTOR at the end

---

## Constraints

- Use only terminating verification commands
- Prefer focused test commands during RED/GREEN, full suite during broader checkpoints
- Do not create a separate mandatory `tdd-plan.md` unless another artifact explicitly requires it
- If the approved implementation plan exists, treat it as the primary source of execution order
