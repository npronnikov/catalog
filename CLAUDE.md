# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repository Is

A **catalog of AI-powered development workflows, skills, and rules** — not application source code. It is consumed by AI coding agents (Qwen, Claude, Codex, Gigacode) via a platform that orchestrates multi-step development processes. The catalog is organized by teams and methodologies.

## Repository Structure

```
flows/    — End-to-end workflow definitions (nodes, transitions, human gates)
skills/   — Reusable AI capabilities invoked within flows or standalone
rules/    — Coding standards and constraints applied during code generation
```

Each entity follows the same versioned structure:

```
<entity-name>/1.0/
├── metadata.yaml   — Identity, ownership, lifecycle, tags
├── FLOW.yaml       — (flows only) Node graph with instructions, transitions, artifacts
├── SKILL.md        — (skills only) Step-by-step instructions for the AI agent
└── RULE.md         — (rules only) Coding conventions and constraints
```

## Entity Metadata Schema (`metadata.yaml`)

Every entity shares these fields: `entity_type` (flow/skill/rule), `id`, `version`, `canonical_name` (`id@version`), `display_name`, `description`, `coding_agent`, `team_code`, `platform_code` (BACK/FRONT), `tags`, `approval_status` (draft/published), `lifecycle_status` (active/inactive).

Key metadata fields by type:
- **Flows**: additionally `flow_kind` (delivery/support), `risk_level`, `scope`
- **Skills**: additionally `skill_kind` (analysis/generation/review)
- **Rules**: additionally `rule_kind` (coding-style), `checksum`, `approved_by`/`approved_at`

## Teams and Methodologies

| Team | Focus | Key Artifacts |
|------|-------|---------------|
| **AIDLC** | Full application design lifecycle (inception → implementation) | Requirements docs, user stories, functional design, C4 architecture, code-gen plans |
| **HGDLC** | Hands-on development cycles with adversarial review | Clarification questions, implementation plans, code review |
| **SpecKit** | Specification-driven development with governance | Constitution, spec, plan, tasks, checklist, analysis |
| **OpenSpec** | Specification-first with delta-specs and archival | Proposals, delta-specs, design, verify, archive |
| **Demo** | Example rules (e.g., Java backend coding standards) | Coding conventions |

## Flow Architecture (FLOW.yaml)

Flows are state machines defined as a list of **nodes** connected by transitions.

### Node types

- **`ai`** — AI agent execution. Requires `routing_type` (`static` or `dynamic`)
- **`command`** — Shell command execution (e.g. `./gradlew test`)
- **`human_input`** — User edits artifacts inline
- **`human_approval`** — Approve/rework gate
- **`terminal`** — End state

### Required handlers per node type

All handlers **must** be in **object form** `{transition: node-id}`. String form (`on_success: node-id`) is **not supported** and will fail validation.

| Node type | Required handlers |
|-----------|-------------------|
| `ai` + `routing_type: static` | `on_success` only |
| `ai` + `routing_type: dynamic` | At least one route-handler (`on_success`, `on_rework`, or custom `on_*`) |
| `command` | `on_success` |
| `human_input` | `on_submit`, `on_rework` |
| `human_approval` | `on_approve`, `on_rework` |
| `terminal` | none |

### Dynamic routing

For `ai` + `routing_type: dynamic`, the AI node must write a `route` field in `step-summary.json` matching one of the declared `on_*` handlers. For `ai` + `routing_type: static`, runtime always uses `on_success` regardless of `step-summary.json` content.

### Rework loops and max_loop_count

- `max_loop_count` applies **only to `ai` and `command` nodes** with cyclic handlers (handlers that transition back to a previous node). It is **required** on such handlers — missing it causes a validation error.
- For `human_input` and `human_approval`, `max_loop_count` is not used and must not be specified.
- When `max_loop_count` is exceeded, runtime falls through to `failure_node_id`.

For `ai`/`command` `on_rework` handlers, three attributes are required:

```yaml
on_rework:
  transition: ai-implement
  max_loop_count: 5
  keep_changes: true                       # required for ai/command on_rework
  session_policy: resume_previous_session   # or new_session; required for ai/command on_rework
```

### Execution context and artifacts

- Each node declares `produced_artifacts` with `scope` (run) and `path`. Artifacts flow between nodes via `execution_context` with `transfer_mode` (`by_ref` / `by_value`)
- `transfer_mode: by_value` is only allowed on `ai` nodes
- `human_input` only supports `by_ref`, only `scope: run`, and must declare `modifiable` on artifact refs
- `human_input` `produced_artifacts` must match the same set of editable artifacts received from predecessor
- `skill_refs` must only appear on `type: ai` nodes

### FTL templating

AI node `instruction` fields support FreeMarker Template Language. Reference previous step outputs via `step.<nodeId>.<field>` and run context via `run.feature_request`, `run.id`, etc.

**Important**: `feature_request` is **not** auto-injected into the system prompt. If an AI node needs the user's original request, include it explicitly via FTL:

```ftl
<#if (run.feature_request)?? && run.feature_request?has_content>
${run.feature_request}
</#if>
```

See `flows/README.md` for FTL usage notes (guard operators, default values).

### Retry policies

Optional on `ai` nodes. `attempts` means **additional** retries after the first failure (not total). If the error is not in `on_errors`, or retries are exhausted, runtime goes to `failure_node_id`.

### Forbidden patterns

- **`on_failure` on `ai`/`command` nodes** — not allowed. Failure fallback always goes to `failure_node_id`.
- **Deprecated fields**: `max_rework_iterations`, `next_node`, `allow_retry` — do not use.

## Skill Structure (SKILL.md)

Skills have a **mandatory** YAML frontmatter block (`name`, `description`) followed by markdown instructions. Missing frontmatter will cause publication to fail (required for Qwen/Gigacode agents).

Recommended structure:
1. `Goal` / `Цель`
2. Step-by-step workflow (`Step 1`, `Step 2`, …)
3. `Output format` with file template
4. `Constraints` (what not to do)

Each skill should produce a **specific named artifact** (e.g. `questions.md`, `requirements.md`, `plan.md`). If output format is not fixed, results will be inconsistent.

If a skill is used in an AI critic loop, its instruction must require writing `route: "on_rework"` or `route: "on_success"` in `step-summary.json`.

## Rule Structure (RULE.md)

Rules have YAML frontmatter (`id`, `version`, `allowed_paths`, `forbidden_paths`, `allowed_commands`, `require_structured_response`) followed by coding standards in markdown. Rules are applied as constraints during code generation tasks.

## Conventions

- **Language**: Flow and skill instructions are primarily in Russian; metadata fields (id, tags) are in English
- **Coding agents**: Most entities target `qwen`; some have variants for `claude`, `codex`, or `gigacode` (e.g., `restore-c4-architecture-java` has three agent-specific variants)
- **Artifact naming**: Flows produce files like `clarification.md`, `spec.md`, `review-report.md`, `implementation-plan.md`, `tasks.md`
- **Checksums**: Rules include a SHA-256 `checksum` field for integrity verification
- **Source examples**: When creating new flows/skills, copy the closest existing example as a base, then adapt `id`, `canonical_name`, `title`, `description`, `instruction`, `skill_refs`

## Validation Checklist

Before creating or modifying a flow, verify:
- All nodes reachable from `start_node_id`; `failure_node_id` is a `terminal` node
- All handlers in object form `{transition: node-id}`
- `ai` nodes have `routing_type`; `ai dynamic` writes `route` in step-summary
- No `on_failure` on `ai`/`command` nodes
- All cyclic `ai`/`command` handlers have `max_loop_count`; `on_rework` has `keep_changes` + `session_policy`
- `skill_refs` only on `ai` nodes
- Artifacts declared and consistent between nodes

Before creating or modifying a skill, verify:
- Frontmatter present with `name` and `description`
- Explicit output format
- Constraints section included
