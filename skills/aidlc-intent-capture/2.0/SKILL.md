---
name: aidlc-intent-capture
description: Use when a raw product idea must be converted into a bounded problem statement, desired outcomes, assumptions, and answerable discovery questions.
---

# AIDLC v2 Intent Capture

## Goal

Describe the problem and intended outcome before evaluating or designing a
solution. Preserve uncertainty instead of turning assumptions into facts.

## Required inputs

- The user's original request and attachments.
- Explicit business, user, timing, legal, and technical constraints.

## Procedure

1. Identify the problem, affected users, current behavior, desired outcome, and
   reason to act now.
2. Separate goals, proposed solutions, constraints, assumptions, and unknowns.
3. Define measurable success signals without inventing target values.
4. Classify the initiative scope and reversibility.
5. Propose a stable lowercase hyphenated `initiative_id`.
6. Generate only questions whose answers can change discovery conclusions.

## Output contract

Write `discovery/intake.md` with problem statement, users, outcomes, non-goals,
constraints, assumptions, success signals, initiative-ID proposal, and source
references.

Write `discovery/questions.md` with numbered questions and one editable
`[Answer]:` field per question. If no questions are needed, state that explicitly
and include an empty-answer checklist that the gate can submit unchanged.

## Quality checks

- The problem statement does not presuppose the requested implementation.
- Assumptions and confirmed facts are visibly distinct.
- Each question explains which decision its answer affects.

## Constraints

- Do not perform research, feasibility analysis, or solution design.
- Do not ask the user directly; produce the gate artifact.
- Use the language of the request.

## Completion

Complete when discovery can proceed without reinterpreting the original request.
