---
name: aidlc-build-test-verification
description: Use when implemented changes require actual build and test execution with preserved commands, exit codes, failures, and a final verdict.
---

# AIDLC v2 Build and Test Verification

## Goal

Establish reproducible evidence that the changed repositories build and satisfy planned acceptance and regression checks.

## Required inputs

- Mutation manifest, implementation plan, acceptance checks, and approved code review.
- Real commands and working directories from `workspace/context.md`.
- Current repository state at the end of implementation.

## Procedure

1. Verify that the mutation manifest matches the current diff.
2. Execute focused tests for each unit in the declared working directory.
3. Execute required builds, type checks, integration tests, migrations, and regressions in dependency order.
4. Record the exact command, working directory, start/finish context, exit code, and observed result.
5. Preserve concise failure excerpts; do not rewrite a nonzero result as success.
6. After primary execution, incorporate an independent foreground test-verifier assessment when linked.
7. Set `PASS` only if every required command and acceptance check passes; otherwise set `REWORK`.

## Output contract

Write run-scoped `feature/test-results.md` with environment/revision, every actual command, exit code, result, relevant failure excerpt, coverage gaps, and exact final `PASS` or `REWORK` verdict.

## Quality checks

- Commands were executed; generated instructions or predicted output are not evidence.
- All planned checks are present or explicitly blocking.
- Failure exit codes and diagnostics are retained accurately.

## Constraints

Do not ask the user directly or fix failures during independent verification. Never claim success from stale or simulated output.

## Completion

Complete when evidence is reproducible and the verdict exactly reflects all required command outcomes.
