---
name: aidlc-platform-foundation-design
description: Use when a team needs an approved, implementation-ready foundation design for environments, delivery, security, recovery, and operations.
---

# AIDLC v2 Platform Foundation Design

## Goal

Define the platform baseline and a safe mutation plan before provisioning or pipeline work begins.

## Required inputs

- `input-manifest.yaml`, `workspace/context.md`, platform request, policies, and current IaC/pipeline/operations evidence.
- Exact repository revisions and target account/environment identifiers.

## Procedure

1. Define the environment matrix, ownership, isolation, promotion path, and lifecycle.
2. Design topology, networking, identity, authorization, encryption, and secret references.
3. Specify build artifacts, deployment strategy, observability, incident ownership, and runbooks.
4. Define data protection, backup, restore, RTO, RPO, capacity, and cost guardrails.
5. Inventory mutations by repository and external target; flag destructive or irreversible actions.
6. Define validation evidence and rollback for every implementation wave.

## Output contract

Write run-scoped:

- `platform/requirements.md`: environment matrix, security, ownership, availability, RTO/RPO, capacity, cost, and measurable acceptance;
- `platform/change-plan.md`: topology, repositories/files, ordered waves, commands, approvals, evidence, stop conditions, and rollback.

## Quality checks

- Every external target and repository is exact, not inferred from a default or latest value.
- Security, recovery, cost, and ownership requirements have acceptance checks.
- Destructive actions are isolated behind explicit approval boundaries.

## Constraints

Do not ask the user directly or mutate project/external state. Never record secret values.

## Completion

Complete when platform mutations can be approved and executed without inventing targets, privileges, safeguards, or success criteria.
