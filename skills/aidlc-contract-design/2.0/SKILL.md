---
name: aidlc-contract-design
description: Use when a solution architecture needs reviewable API, event, message, or schema contracts before implementation.
---

# AIDLC v2 Contract Design

## Goal

Turn integration and persistence boundaries into concrete, versionable contracts.

## Required inputs

- Approved requirements, domain model, architecture, and pinned workspace context.
- Existing contract files explicitly listed in `input-manifest.yaml`.

## Procedure

1. Inventory every boundary that exchanges commands, queries, events, or persisted data.
2. Define concrete machine-readable API, event, or schema files where applicable.
3. Specify validation, authentication/authorization, error taxonomy, timeouts, and partial failure behavior.
4. Define idempotency keys and replay/duplicate behavior for mutating or asynchronous operations.
5. Assess backward/forward compatibility, evolution rules, deprecation, and versioning.
6. Index every contract with owners, consumers, and requirement IDs.

## Output contract

Write run-scoped `solution/contracts/index.md` plus concrete files beneath `solution/contracts/`. The index records compatibility, errors, idempotency, versioning, ownership, consumers, and requirement traceability for every contract.

## Quality checks

- Examples validate against their declared schemas.
- Required and optional fields, nullability, units, ordering, and error shapes are unambiguous.
- Compatibility breaks and migration requirements are explicit blockers.

## Constraints

Do not ask the user directly. Do not replace concrete contracts with prose when a formal representation is feasible.

## Completion

Complete when every architecture boundary has either a concrete indexed contract or an explicit not-applicable rationale.
