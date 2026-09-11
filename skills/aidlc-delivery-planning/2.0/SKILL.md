---
name: aidlc-delivery-planning
description: Use when solution units must become an ordered, gated delivery plan with complete requirement-to-check traceability.
---

# AIDLC v2 Delivery Planning

## Goal

Turn the approved solution package into an executable sequence with explicit evidence and approval gates.

## Required inputs

- `solution/requirements.md`, `solution/units.yaml`, all design artifacts, and workspace commands.
- Risks and dependencies from the pinned input set.

## Procedure

1. Validate the unit waves and identify contract, migration, feature-flag, and environment prerequisites.
2. Sequence test-first implementation, integration, migration, rollout, and rollback steps.
3. Define entry/exit criteria, responsible role, commands, evidence, and approval gates per wave.
4. Map each requirement to one or more units and executable checks.
5. Map cross-cutting NFR checks and negative paths to the appropriate wave.
6. Report every uncovered requirement, unowned risk, or unverifiable unit as blocking.

## Output contract

Write run-scoped:

- `solution/delivery-plan.md`: waves, steps, gates, commands, evidence, rollback boundaries, dependencies, and risks;
- `solution/traceability.yaml`: requirement-to-unit-to-check coverage with explicit blocking gaps.

## Quality checks

- Every requirement ID has at least one owning unit and acceptance check.
- The plan respects the unit DAG and includes failure/rollback paths.
- Every uncovered requirement is marked blocking; percentages cannot hide gaps.

## Constraints

Do not ask the user directly or start implementation. Never invent commands that contradict `workspace/context.md`.

## Completion

Complete only when traceability has no unreported gaps and each wave has executable entry and exit criteria.
