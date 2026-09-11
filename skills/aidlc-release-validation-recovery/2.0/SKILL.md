---
name: aidlc-release-validation-recovery
description: Use after deployment or rollback to observe exact acceptance criteria, choose the required route, and preserve an immutable outcome record.
---

# AIDLC v2 Release Validation and Recovery

## Goal

Decide from observed production-state evidence whether to accept the release or request approved rollback, then verify the final state.

## Required inputs

- Input manifest, readiness report, deployment log, approved health thresholds, observation window, and rollback criteria.
- Foreground safety and platform-review results when linked.
- Rollback execution evidence when verifying recovery.

## Procedure

1. Reconfirm candidate and target identity without mutation.
2. Run approved smoke, health, telemetry, functional, migration, and compatibility checks.
3. Observe the full window and record commands, exit codes, timestamps, measurements, and alerts.
4. Select `on_accept` only when every mandatory criterion passes; otherwise select `on_rollback` and request the human rollback gate.
5. After rollback, verify restored version/state, data consistency, health, and residual impact.
6. Reconcile independent reviews and record unresolved operational follow-up.

## Output contract

Write run-scoped `release/validation-results.md`; when rollback occurs also write `release/rollback-report.md`; always write `release/outcome.yaml` with exact final outcome `ACCEPTED` or `ROLLED_BACK`, identities, evidence links, times, and residual risks.

## Quality checks

- Results come from actual observation, not a deployment command's success alone.
- Route and outcome match thresholds and retained exit codes.
- A failed or partial rollback cannot be labeled `ROLLED_BACK` successfully.

## Constraints

Do not ask the user directly, deploy, or initiate rollback before its human gate. Never expose secrets.

## Completion

Complete when the final observed state and outcome are unambiguous, evidence-backed, and ready for immutable publication.
