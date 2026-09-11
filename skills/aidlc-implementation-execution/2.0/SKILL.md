---
name: aidlc-implementation-execution
description: Use when approved implementation units must be applied to real repository files and evidenced with tests and a mutation manifest.
---

# AIDLC v2 Implementation Execution

## Goal

Implement the approved change faithfully while keeping mutations bounded, attributable, and testable.

## Required inputs

- Approved `feature/implementation-plan.md`, change design, and acceptance checks.
- Exact repository revisions and project rules from `input-manifest.yaml`.
- One allocated unit at a time when delegated to an implementer.

## Procedure

1. Verify the unit, dependencies, allowed repository, and file ownership before editing.
2. Run or add the planned failing test and record the observed red state.
3. Make the minimum production mutation needed for the unit.
4. Run focused checks, then relevant regression checks; preserve actual outcomes.
5. Compare changed paths with the allocated scope and explain approved deviations.
6. Update the mutation manifest after each completed unit and stop on blockers.

## Output contract

Produce real project-scoped source, test, schema, migration, or configuration mutations and run-scoped `feature/mutation-manifest.yaml` containing unit IDs, concrete changed paths, change purpose, commands, outcomes, deviations, and blockers.

## Quality checks

- At least one changed project path is outside `aidlc-docs/**`.
- Every changed path belongs to a completed unit or an explicit approved deviation.
- Tests demonstrate the requested behavior and preserve failure exit codes.

## Constraints

Do not ask the user directly. Documentation-only edits do not satisfy implementation. Never report success if no real non-documentation project mutation occurred.

## Completion

Complete only when all executable units have verified real mutations and the manifest matches the working tree.
