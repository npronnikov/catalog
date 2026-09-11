---
name: aidlc-requirements-specification
description: Use when answered clarification and approved discovery inputs must become a traceable, measurable requirements baseline.
---

# AIDLC v2 Requirements Specification

## Goal

Create an implementation-independent baseline that can be approved and traced through design, delivery, and tests.

## Required inputs

- `input-manifest.yaml`, approved discovery artifacts, and answered `solution/questions.md`.
- Workspace constraints and applicable policies.

## Procedure

1. Reconcile answers with discovery scope and record contradictions as blockers.
2. Assign stable IDs by class: `FR`, `API`, `DM`, `AUTH`, `NFR`, and `TEST`.
3. State each requirement as observable behavior, rule, constraint, or quality threshold.
4. Give every requirement measurable acceptance criteria and source trace.
5. Record assumptions, exclusions, dependencies, and unresolved decisions separately.
6. Define terminology and avoid architecture choices unless they are approved constraints.

## Output contract

Write run-scoped `solution/requirements.md` with:

- context, scope, actors, terminology, and constraints;
- uniquely identified functional, interface, data, authorization, quality, and test requirements;
- measurable acceptance criteria for every requirement;
- assumptions, dependencies, exclusions, conflicts, and open blockers;
- traceability back to discovery and clarification entries.

## Quality checks

- IDs are unique and stable within the initiative.
- Criteria are testable and do not use unqualified terms such as fast, secure, or scalable.
- Every answered question is reflected or explicitly rejected with rationale.

## Constraints

Do not ask the user directly or fabricate decisions. Missing approval-relevant information remains a blocker for the human gate.

## Completion

Complete when the requirements form a coherent approval candidate with measurable coverage and no concealed ambiguity.
