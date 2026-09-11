---
name: aidlc-rough-mockups
description: Use when discovery scope includes user interaction that benefits from a low-fidelity journey, state, or screen concept before a product decision.
---

# AIDLC v2 Rough Mockups

## Goal

Make proposed user interaction concrete enough to expose scope and usability
assumptions without creating production design.

## Required inputs

- `discovery/intake.md`.
- `discovery/scope-draft.md`.
- Confirmed users, workflows, constraints, and success measures.

## Procedure

1. Decide whether an interaction mockup materially reduces uncertainty.
2. If applicable, model the primary journey plus empty, loading, error,
   permission-denied, and recovery states.
3. Use low-fidelity Mermaid, text wireframes, or linked generated images.
4. Annotate each view with the scope assumption it tests.
5. If not applicable, explain why no user-facing interaction is involved.

## Output contract

Write `discovery/mockups/mockups.md` with:

- `status: produced` or `status: not_applicable`;
- journey and state inventory;
- low-fidelity mockups when produced;
- accessibility and content assumptions;
- questions revealed by the mockups;
- links to any additional run-scoped image artifacts.

## Quality checks

- Error and recovery behavior are represented, not only the happy path.
- Mockups do not silently expand the approved scope.
- `not_applicable` includes a concrete rationale.

## Constraints

- Do not implement UI or create a design system.
- Do not present low-fidelity output as approved visual design.
- Do not ask the user directly.

## Completion

Complete when the output either tests the relevant interaction assumptions or
demonstrates that mockups add no value.
