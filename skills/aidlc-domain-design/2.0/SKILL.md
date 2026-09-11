---
name: aidlc-domain-design
description: Use when approved requirements need domain boundaries and an architecture that preserves responsibilities, invariants, and lifecycle behavior.
---

# AIDLC v2 Domain and Architecture Design

## Goal

Produce a coherent solution model whose decisions can be reviewed independently of code generation.

## Required inputs

- Approved `solution/requirements.md` and applicable `solution/user-stories.md`.
- `workspace/context.md` and exact repository revisions from `input-manifest.yaml`.

## Procedure

1. Identify capabilities, bounded responsibilities, actors, entities, values, and services.
2. Define important states, transitions, invariants, ownership, and consistency boundaries.
3. Map required behavior to components and dependencies already present or proposed.
4. Document data flows, trust boundaries, failure behavior, and integration direction.
5. Capture consequential choices as ADR-style decisions with alternatives and tradeoffs.
6. Link every architectural element to requirement IDs.

## Output contract

Write run-scoped:

- `solution/domain-model.md`: vocabulary, boundaries, entities, states, invariants, ownership, and domain relationships;
- `solution/architecture.md`: components, responsibilities, dependencies, data flows, trust boundaries, failure behavior, and ADR-style decisions.

## Quality checks

- Cyclic dependencies and conflicting ownership are absent or explicitly blocked.
- State transitions preserve declared invariants.
- Decisions include alternatives, consequences, and requirement traceability.

## Constraints

Do not ask the user directly. Do not infer implementation facts outside the pinned repository revision.

## Completion

Complete when another engineer can explain boundaries, runtime interactions, failure behavior, and decision rationale from the two artifacts.
