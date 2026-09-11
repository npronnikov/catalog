---
name: aidlc-feasibility-and-scope
description: Use when discovery evidence must be converted into a feasibility assessment, bounded MVP scope, risks, dependencies, and explicit stop conditions.
---

# AIDLC v2 Feasibility and Scope

## Goal

Determine whether a useful, supportable initiative can be delivered within the
known constraints and define the smallest outcome worth pursuing.

## Required inputs

- Approved discovery intake and answers.
- `discovery/research.md` and its independent assessment.
- Known business, technical, security, legal, operational, and timing limits.

## Procedure

1. Assess desirability, technical feasibility, organizational feasibility,
   operational sustainability, compliance, cost, schedule, and reversibility.
2. Rate impact and likelihood for every material risk and name a mitigation or
   stop condition.
3. Define MVP in outcome terms, then list in-scope and out-of-scope behavior.
4. Identify dependencies, experiments, and decisions required before delivery.
5. Make uncertainty visible and avoid false precision in estimates.

## Output contract

Write `discovery/feasibility.md` with dimension-by-dimension evidence, risk
matrix, dependencies, experiments, stop conditions, and an overall
`FEASIBLE`, `CONDITIONAL`, or `NOT_FEASIBLE` assessment.

Write `discovery/scope-draft.md` with target users, MVP outcomes, in-scope,
out-of-scope, success measures, constraints, assumptions, and change triggers.

## Quality checks

- Each scope item traces to an outcome or constraint.
- Each critical risk has an owner decision or stop condition.
- `NOT_FEASIBLE` is not followed by an unexplained delivery scope.

## Constraints

- Do not design architecture or implementation tasks.
- Do not invent budgets, deadlines, staffing, or compliance decisions.
- Do not ask the user directly.

## Completion

Complete when the next decision can accept, reject, or defer a clearly bounded
initiative.
