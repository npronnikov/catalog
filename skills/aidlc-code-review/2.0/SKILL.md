---
name: aidlc-code-review
description: Use when completed repository mutations need an independent, evidence-based review and an APPROVE or REWORK verdict.
---

# AIDLC v2 Code Review

## Goal

Find correctness, regression, security, maintainability, and traceability defects before build/test acceptance.

## Required inputs

- Approved change design and implementation plan.
- Current diff, mutation manifest, tests, and pinned baseline revision.
- Foreground code-reviewer result when the flow links that subagent.

## Procedure

1. Reconcile the diff with planned units and identify missing or unplanned mutations.
2. Trace changed behavior to requirements, contracts, data rules, NFRs, and tests.
3. Inspect error paths, boundaries, concurrency, authorization, migrations, and compatibility.
4. Report only actionable findings with severity, exact file/line evidence, impact, and required fix.
5. Separate blocking defects from non-blocking observations and verification gaps.
6. Set verdict `REWORK` for any blocking finding; otherwise set `APPROVE`.

## Output contract

Write run-scoped `reviews/code-review.md` with reviewed revision/diff, severity-ranked findings, file/line evidence, impact, required fixes, verification gaps, and exact `APPROVE` or `REWORK` verdict.

## Quality checks

- Findings are specific, reproducible, and tied to changed code or a missing required change.
- The report accounts for every implementation unit and unexplained mutation.
- Verdict matches the highest-severity unresolved finding.

## Constraints

Do not ask the user directly and never fix the code being reviewed. Missing evidence is a review gap, not permission to assume success.

## Completion

Complete when every blocking concern is actionable and the verdict can drive the flow without interpretation.
