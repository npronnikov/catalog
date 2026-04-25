---
name: hgdlc-implementation-plan
description: >
  Analyse available context (requirements, design docs, task description) and
  the current codebase, then produce a concrete checkbox-driven implementation
  plan that Claude Code can execute step by step without re-reading artifacts.
---

# Implementation Plan

## Goal
Produce a numbered, checkbox-driven `implementation-plan.md` that is concrete
enough for Claude Code to execute mechanically: every file to touch, in the
correct order, with verification commands after each layer.

---

## Step 1 — Read Available Context

Load what is available (not all sources may exist):

**Requirements / design artifacts** (load if present):
- Task description or feature request (from the node instruction)
- `*/requirements.md`
- `*/application-design.md`
- `*/functional-design/*.md`

**Codebase exploration** (always do this):
1. Find build system: look for `build.gradle`, `pom.xml`, `package.json`, `pyproject.toml`
2. Find entry points and package / module structure
3. Find existing patterns for the layer being changed:
   - Controllers / routes already in the project
   - Service / use-case classes already in the project
   - Entity / model classes already in the project
   - Migration files (Liquibase changelogs, Flyway scripts, Alembic)
   - Test examples (unit and integration)
4. Find configuration files that may need updating

Collect: **what exists** and **what must be created or modified**.

---

## Step 2 — Identify All Affected Files

For each file, determine:
- **Action**: `Create` or `Modify`
- **Layer**: schema | domain | repository | service | api | test-unit | test-integration | config | frontend
- **Why**: which requirement or design decision drives this change

Group by layer. Within a layer, order from least-dependent to most-dependent.

For **Modify** files: identify the exact method, field, or block to change —
do not plan a full rewrite unless truly necessary.

---

## Step 3 — Order Steps by Dependency

Build the execution sequence from foundation to surface:

| Order | Layer | What to do |
|-------|-------|-----------|
| 1 | **Schema migrations** | DDL changes — new tables, columns, indexes, constraints |
| 2 | **Domain entities / models** | Classes, fields, relationships, validations |
| 3 | **Repositories / DAOs** | Interfaces, custom queries, data access |
| 4 | **Services / use-cases** | Business logic, orchestration, transactions |
| 5 | **API layer** | Controllers, DTOs, request/response records, routes |
| 6 | **Unit tests** | Per-layer unit tests, immediately after each layer |
| 7 | **Integration tests** | Cross-layer scenarios, full request–response cycles |
| 8 | **Configuration** | ENV vars, Spring profiles, feature flags, secrets |
| 9 | **Frontend** | Components, pages, API calls — after backend API is stable |

Add a **Build & verify** step after every layer that produces compilable code.

---

## Step 4 — Write the Plan

Save to `implementation-plan.md` (path relative to the project root, or as
specified in the node instruction). Follow the format below **exactly**.

**Rules for step descriptions:**
- Every checkbox maps to one file action or one shell command.
- File paths must be exact and relative to the workspace root.
- For `Modify`: describe *what* to add/change, not the full file content.
- For `Create`: describe the new class / function / schema with key fields.
- Verification commands must be real commands runnable in the project shell.
- Do NOT write actual code — only describe structure, fields, and behaviour.

---

## Output Format

```markdown
# Implementation Plan

## Context
- **Task**: [one-sentence summary of what is being built]
- **Project type**: Brownfield / Greenfield
- **Workspace root**: [path]
- **Build system**: Gradle / Maven / npm / other
- **Scope**: [which layers are touched]

---

## Step 1 — [Layer name] (complexity: low / medium / high)
**Goal**: [what this step achieves and why it comes first]

- [ ] Create / Modify `path/to/file.ext`
  - [specific: add column X of type Y | add method foo(Bar bar): Baz | add field Z]
- [ ] Create / Modify `path/to/another.ext`
  - [specific description]

**Verify**: `./gradlew compileJava` — must succeed with no errors

---

## Step 2 — [Layer name] (complexity: ...)
**Goal**: [what this step achieves]

- [ ] Create / Modify `path/to/file.ext`
  - [specific description]

**Verify**: `./gradlew test --tests "ClassName"` — all tests green

---

... (one section per layer, only layers that have actual work)

---

## Full Build Verification
- [ ] Run `[full build command]` — zero errors, zero test failures
- [ ] Run `[linter / format check]` if applicable

## Risk Notes
- [Risk]: [what to watch for, potential conflict, or rollback consideration]

## Traceability
| Requirement | Implemented in steps |
|-------------|---------------------|
| FR-001      | 1, 3, 4, 6          |
| API-002     | 4, 5, 7             |
```

---

## Constraints

- Every step must have at least one `[ ]` checkbox.
- Brownfield: mark every file explicitly as `Create` or `Modify`; never plan
  copies like `ClassName_modified.java`.
- Do not plan steps for layers with no actual changes.
- The plan must be self-contained — Claude Code must be able to execute it
  without re-reading any design artifact.
- No actual source code in the plan — descriptions only.
- Language: match the language of the task description / node instruction.
