---
name: aidlc-change-impact-design
description: Use when a bounded change request needs evidence-based impact analysis and a repository-specific change design before implementation.
---

# AIDLC v2 Change Impact Design

## Goal

Define the smallest coherent change that satisfies acceptance criteria without breaking existing contracts or qualities.

## Required inputs

- `input-manifest.yaml`, `workspace/context.md`, request, and answered change questions.
- The exact optional solution package path selected by the manifest.
- Source and test trees at the pinned revisions.

## Procedure

1. Trace requested behavior and acceptance criteria to existing code, contracts, data, tests, and operations.
2. Distinguish confirmed impact from hypotheses and record file/section evidence.
3. Analyze compatibility, authorization, migration, concurrency, failure, and NFR consequences.
4. Design changed responsibilities, interfaces, states, data transformations, and rollout boundaries.
5. Define exact positive, negative, integration, migration, and regression checks.
6. Incorporate foreground architecture-review findings; unresolved blockers remain visible.

## Output contract

Write run-scoped:

- `feature/impact-analysis.md`: affected code, contracts, data, NFRs, dependencies, risks, evidence, and unknowns;
- `feature/change-design.md`: target behavior, responsibility and contract deltas, compatibility/migration strategy, rollback boundary, and exact acceptance checks.

## Quality checks

- Every proposed change traces to a requirement or acceptance criterion.
- Every compatibility and data risk has mitigation and verification.
- Unaffected areas are not claimed without repository evidence.

## Constraints

Do not ask the user directly or mutate source. Use only paths and revisions pinned in the manifest.

## Completion

Complete when implementation scope and checks are concrete, bounded, and free of unresolved architectural blockers.
