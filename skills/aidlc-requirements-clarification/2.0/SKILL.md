---
name: aidlc-requirements-clarification
description: Use when an approved initiative must be converted into an exhaustive, answerable requirements questionnaire before solution design.
---

# AIDLC v2 Requirements Clarification

## Goal

Expose material requirement gaps without prematurely choosing an implementation.

## Required inputs

- `input-manifest.yaml` and `workspace/context.md`.
- The exact approved discovery package selected by the manifest.
- Applicable project constraints and policies.

## Procedure

1. Trace the problem, users, outcomes, scope, constraints, and success measures.
2. Inspect the selected repository revision only for facts needed to phrase questions.
3. Group unanswered questions by functional behavior, data, APIs/events, authorization, quality, operations, migration, and verification.
4. Make each question atomic and include why its answer affects acceptance or design.
5. Add a literal `[Answer]:` field after every question.
6. Separate blocking questions from assumptions that can safely remain explicit.
7. Do not disguise a proposed technology or architecture choice as a question.

## Output contract

Write run-scoped `solution/questions.md` containing:

- source artifact paths and revisions;
- numbered questions grouped by concern;
- a `[Answer]:` field for every question;
- impact and blocking status for every unanswered item;
- explicit assumptions and conflicts found in the inputs.

## Quality checks

- Every requirement dimension has been considered or marked not applicable with rationale.
- Questions are non-duplicative, neutral, and answerable by a decision owner.
- No answer is invented and no unresolved blocker is hidden in prose.

## Constraints

Do not ask the user directly. The flow's human-input gate collects answers in the declared artifact. Use only inputs pinned by `input-manifest.yaml`.

## Completion

Complete when every question has an answer slot and all material omissions are classified as blocking or explicit assumptions.
