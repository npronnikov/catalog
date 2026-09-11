---
name: aidlc-units-decomposition
description: Use when an approved solution must be divided into dependency-safe implementation units with explicit inputs, outputs, files, and checks.
---

# AIDLC v2 Units Decomposition

## Goal

Create independently verifiable units that can be executed in deterministic dependency waves.

## Required inputs

- Requirements, stories if applicable, domain and architecture design, contracts, NFRs, and infrastructure impact.
- Exact repositories and revisions from `input-manifest.yaml`.

## Procedure

1. Partition work by coherent responsibility and verifiable outcome, not arbitrary file count.
2. Assign stable unit IDs and list requirement IDs covered by each unit.
3. Declare dependencies, repository, candidate files, inputs, outputs, risks, and rollback boundary.
4. Define concrete acceptance checks and commands for every unit.
5. Topologically sort dependencies into execution waves.
6. Perform an explicit DAG cycle check; reject cycles, missing dependencies, and duplicate ownership.
7. Reject any unit without at least one acceptance check.

## Output contract

Write run-scoped `solution/units.yaml` containing unit IDs, responsibility, requirement links, dependencies, inputs, outputs, repository/files, acceptance checks, rollback boundary, and acyclic execution waves.

## Quality checks

- The dependency graph passes a cycle check and every referenced unit exists.
- Every unit has bounded ownership, observable output, and acceptance checks.
- Cross-repository ordering and contract-first prerequisites are explicit.

## Constraints

Do not ask the user directly or execute units. Do not create placeholder units with unbounded file ownership.

## Completion

Complete only when the graph is acyclic and every unit can be assigned and verified independently.
