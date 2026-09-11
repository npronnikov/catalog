---
name: aidlc-release-readiness
description: Use when an immutable release candidate and exact target require an evidence-based READY or BLOCKED decision before human deployment approval.
---

# AIDLC v2 Release Readiness

## Goal

Prevent deployment until candidate identity, target identity, dependencies, migrations, validation, and rollback are demonstrably ready.

## Required inputs

- Root `input-manifest.yaml` with candidate digest/version, repository revision, target environment, deployment procedure, and rollback procedure.
- Approved delivery records, build/test/security evidence, change approvals, and current target state.
- Foreground safety and platform review results when linked.

## Procedure

1. Verify candidate identity is immutable and matches all supplied evidence.
2. Verify exact account/project/cluster/environment and current target version through read-only inspection.
3. Check dependencies, capacity, maintenance window, ownership, communications, and freeze constraints.
4. Review data migrations for compatibility, duration, backup, verification, and irreversibility.
5. Validate deployment checks, health thresholds, observation window, abort criteria, and rollback commands.
6. Reconcile independent review findings; any unresolved blocker yields `BLOCKED`.

## Output contract

Write run-scoped `release/readiness-report.md` with candidate identity, target identity, evidence inventory, dependency/migration/rollback checks, blockers, approvals required, and exact `READY` or `BLOCKED` verdict.

## Quality checks

- Candidate and target identities are explicit and cross-checked, never inferred from latest/default.
- All evidence references a compatible revision and is not stale.
- Rollback has an owner, trigger, executable procedure, and data consequence.

## Constraints

Do not ask the user directly, deploy, or rollback. Missing identity or procedure is always blocking. Never include credentials.

## Completion

Complete when the report supports a human deployment decision without relying on implicit state.
