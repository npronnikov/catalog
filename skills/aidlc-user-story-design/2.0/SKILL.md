---
name: aidlc-user-story-design
description: Use when approved requirements should be organized into user-centered, independently testable delivery slices.
---

# AIDLC v2 User Story Design

## Goal

Describe valuable user-visible slices without losing requirement-level precision.

## Required inputs

- Approved run-scoped `solution/requirements.md`.
- Discovery outcome and workspace context selected by `input-manifest.yaml`.

## Procedure

1. Decide whether story form is appropriate for the initiative.
2. If appropriate, group behavior by actor goal and outcome, not technical layer.
3. Give each story a stable ID and link all covered requirement IDs.
4. Add concrete acceptance examples, error paths, permission cases, and relevant quality criteria.
5. Report requirements not covered by a story and explain where they will be verified.

## Output contract

Write run-scoped `solution/user-stories.md` containing either:

- `status: applicable`, story IDs, actors, outcomes, requirement links, dependencies, and testable acceptance criteria; or
- `status: not_applicable`, rationale, and the alternative requirement-to-delivery organization.

## Quality checks

- Every story traces to at least one approved requirement.
- Acceptance criteria are observable and include negative paths where relevant.
- No requirement is silently dropped or invented.

## Constraints

Do not ask the user directly. Do not use stories to override the approved requirement baseline.

## Completion

Complete when story applicability is explicit and all requirement coverage is accounted for.
