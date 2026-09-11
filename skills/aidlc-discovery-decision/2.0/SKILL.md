---
name: aidlc-discovery-decision
description: Use when discovery evidence, feasibility, scope, and optional mockups must be synthesized into a reviewable GO, NO-GO, or DEFER decision.
---

# AIDLC v2 Discovery Decision

## Goal

Create one transparent decision package that a human can approve without
reconstructing reasoning from scattered discovery notes.

## Required inputs

- Intake and answered discovery questions.
- Research and independent research assessment.
- Feasibility, scope, risk, and optional mockup artifacts.

## Procedure

1. Validate that all inputs refer to the same initiative and are current.
2. Reconcile evidence, uncertainty, feasibility, scope, and stop conditions.
3. Select exactly one recommendation: `GO`, `NO-GO`, or `DEFER`.
4. Explain why alternatives were rejected and what could change the decision.
5. For `GO`, define measurable outcomes and the approved scope boundary.
6. For `DEFER`, state the evidence or decision needed to resume.

## Output contract

Write `discovery/decision-draft.yaml`:

```yaml
schema_version: 2
initiative_id: stable-id
decision: GO
confidence: medium
blocking_unknowns: []
required_next_flow: null
```

Write `discovery/initiative-brief.md` with context, problem, users, evidence,
scope, non-goals, feasibility, risks, success measures, recommendation, rejected
alternatives, and approval checklist.

## Quality checks

- The YAML decision and brief recommendation agree.
- The decision follows the evidence and does not hide blocking uncertainty.
- A `GO` brief is usable as optional input to solution planning.

## Constraints

- Do not approve the decision or publish project artifacts.
- Do not convert an independent `BLOCKED` finding to passing without evidence.
- Do not ask the user directly.

## Completion

Complete when the human gate can make one explicit decision from the package.
