---
name: aidlc-readiness-review
description: Use when an AIDLC v2 artifact package needs a final evidence-based completeness, consistency, traceability, and freshness decision before approval or publication.
---

# AIDLC v2 Readiness Review

## Goal

Decide whether the supplied package is safe to hand off. Missing evidence is a
finding, not an invitation to infer that work was completed.

## Required inputs

- The exact artifact manifest for the current flow.
- Requirements, decisions, plans, reviews, and execution evidence declared by
  that manifest.
- Repository revisions recorded in `input-manifest.yaml`.

## Procedure

1. Confirm every required artifact exists and belongs to the selected intent,
   change, platform baseline, or release.
2. Check source revisions and flag stale inputs.
3. Walk traceability from requirements through design, units, mutations, and
   verification where those layers apply.
4. Find contradictions, unsupported claims, unresolved blocking findings,
   out-of-scope changes, and evidence gaps.
5. Separate blocking findings from non-blocking observations.
6. Issue `READY` only when every blocking item is resolved by present evidence.

## Output contract

Write the flow-specific `readiness-report.md`:

```markdown
# Readiness Report

## Verdict
READY | BLOCKED

## Inputs and freshness
## Traceability coverage
## Blocking findings
## Non-blocking observations
## Evidence inspected
## Required next action
```

Each finding identifies the artifact and section, impact, and concrete required
correction. An empty blocking section must say `None`.

## Quality checks

- `READY` and a non-empty blocking list cannot coexist.
- Claims cite inspected artifacts or repository evidence.
- The report does not silently downgrade another reviewer's blocking verdict.

## Constraints

- Do not change the reviewed artifacts.
- Do not approve based on intent or prose summaries without evidence.
- Do not ask the user directly; route missing decisions through the flow.

## Completion

Complete only with one unambiguous verdict and a reproducible evidence list.
