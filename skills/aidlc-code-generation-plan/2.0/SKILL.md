---
name: aidlc-code-generation-plan
description: Use when an approved change design must become an ordered, test-first implementation plan grounded in exact repository files.
---

# AIDLC v2 Code Generation Plan

## Goal

Produce executable implementation units that can be delegated, verified, and safely reworked.

## Required inputs

- Change request, `feature/impact-analysis.md`, and `feature/change-design.md`.
- Workspace conventions, exact repository revisions, and optional approved solution units.

## Procedure

1. Recheck candidate files and test locations against the pinned repository tree.
2. Split work into coherent units with explicit ownership and dependencies.
3. For each behavior, place a failing test or executable check before production changes.
4. List exact paths, symbols or configuration areas, commands, expected failure, and expected success.
5. Include contract, schema, migration, feature-flag, documentation, and cleanup steps when applicable.
6. Define rollback boundaries and stop conditions for irreversible changes.

## Output contract

Write run-scoped `feature/implementation-plan.md`, ordered by unit, with requirement links, dependencies, exact files, test-first steps, commands, expected results, risks, and rollback boundaries.

## Quality checks

- Each unit is independently verifiable and owns non-overlapping files unless sequencing is explicit.
- Every acceptance criterion maps to an executable check.
- Commands conform to `workspace/context.md`; vague steps such as update as needed are prohibited.

## Constraints

Do not ask the user directly or implement changes. Treat unresolved file ownership or missing test strategy as blocking.

## Completion

Complete when an implementer can execute every unit without inventing scope, paths, commands, or acceptance behavior.
