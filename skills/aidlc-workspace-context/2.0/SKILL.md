---
name: aidlc-workspace-context
description: Use when an AIDLC v2 flow must establish exact repositories, revisions, project inputs, build commands, and working conventions before analysis or delivery.
---

# AIDLC v2 Workspace Context

## Goal

Create a reproducible execution boundary before any planning or mutation. Never
guess which repository, revision, document, or command is authoritative.

## Required inputs

- The flow request and attachments.
- The checked-out repository roots and current revisions.
- Project rules and build configuration present in the workspace.
- Explicit project artifact paths supplied by the request, if any.

## Procedure

1. Inventory every repository alias, root, and immutable revision.
2. Identify languages, build tools, package managers, test runners, deployment
   tools, and repository-local instructions from actual files.
3. Record exact commands supported by the workspace; do not invent commands.
4. Resolve only explicitly selected project artifacts. Never choose an arbitrary
   latest requirements, design, change, or release document.
5. Note missing inputs, stale references, repository mismatches, and safety
   constraints.
6. Propose a lowercase hyphenated intent/change/release identifier when the flow
   needs one; keep the human-approved identifier authoritative.

## Output contract

Write root `input-manifest.yaml` with:

```yaml
schema_version: 2
intent_id: null
change_id: null
release_id: null
repositories:
  - alias: main
    revision: immutable-revision
source_artifacts: []
target_environment: null
```

Write `workspace/context.md` containing repository evidence, build and test
commands, conventions, selected input documents, risks, and unresolved inputs.

## Quality checks

- Every repository has an alias and concrete revision.
- Every command is supported by a checked-in configuration file.
- Every project artifact path is exact and traceable to the request.
- Unknown values remain explicit `null` or blocking items, never guesses.

## Constraints

- Do not mutate project files.
- Do not expose credentials or secret values.
- Do not ask the user directly; write missing information for a flow gate.
- Produce output in the language of the request.

## Completion

Complete only when both declared files exist and another node can reproduce the
selected context without rediscovery.
