---
name: aidlc-opportunity-research
description: Use when an approved discovery intent needs evidence about alternatives, users, market or internal context, and uncertainty before feasibility and scope decisions.
---

# AIDLC v2 Opportunity Research

## Goal

Produce decision-grade evidence without presenting hypotheses or stale material
as verified facts.

## Required inputs

- `discovery/intake.md` and answered `discovery/questions.md`.
- The foreground result from `aidlc-qwen-research-analyst`.
- Available internal documents and externally cited sources.

## Procedure

1. Derive research questions from the decisions the discovery flow must make.
2. Evaluate current alternatives, including doing nothing and internal reuse.
3. Record source URL or project path, publication/update date, access date, and
   which claim each source supports.
4. Distinguish observed evidence, inference, and hypothesis.
5. Reconcile conflicting sources and explain the selected interpretation.
6. Assign high, medium, or low confidence to each conclusion.

## Output contract

Write `discovery/research.md` containing research questions, alternatives,
evidence table, user/problem signals, constraints, contradictions, conclusions,
confidence, and remaining unknowns.

Write `reviews/research-assessment.md` from the returned foreground subagent
result, preserving its sources, gaps, and `PASS` or `BLOCKED` verdict.

## Quality checks

- Every factual claim has a source or is explicitly marked unverified.
- Time-sensitive evidence includes a date.
- A blocking research verdict cannot be rewritten as passing.

## Constraints

- Do not decide `GO`, `NO-GO`, or product scope.
- Do not fabricate citations, market sizes, users, or competitors.
- Do not ask the user directly.

## Completion

Complete when every research question has evidence, an explicit unknown, or a
documented reason it cannot affect the decision.
