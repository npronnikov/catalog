---
name: aidlc-release-execution
description: Use when a human-approved immutable candidate or rollback must be executed once against an exact environment with a complete non-secret log.
---

# AIDLC v2 Release Execution

## Goal

Execute only the authorized external-state transition and preserve enough evidence to validate or recover it.

## Required inputs

- `input-manifest.yaml`, `release/readiness-report.md` with `READY`, and explicit human approval evidence.
- Exact candidate, target, procedure, expected current state, abort criteria, and rollback procedure.

## Procedure

1. Use safe read-only commands to revalidate target identity, current version, candidate identity, and authorization.
2. Stop if any identity differs, approval is absent/stale, or the procedure is ambiguous.
3. Execute the approved deployment or rollback command exactly once; do not automatically retry destructive or non-idempotent actions.
4. Record timestamps, redacted command, exit code, observable state transition, and partial-failure indicators.
5. Stop further mutation at the approved abort condition and hand evidence to validation/recovery.

## Output contract

Write run-scoped `release/deployment-log.md` for deployment or append a distinct rollback execution section when directed, containing identities, approval reference, timestamps, redacted command, exit code, observed state, and deviations.

## Quality checks

- Read-only verification is clearly separated from external mutation.
- The executed target and candidate exactly match approval.
- No credential, token, secret value, or sensitive environment dump is logged.

## Constraints

Do not ask the user directly. Never execute without approval evidence or exact target identity, and never automatically retry deployment or rollback.

## Completion

Complete when one authorized operation has a faithful result record, including partial or failed outcomes.
