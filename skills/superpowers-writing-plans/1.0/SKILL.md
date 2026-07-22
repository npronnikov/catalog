---
name: superpowers-writing-plans
description: >
  Produce a concrete, self-contained implementation plan before touching any code.
  Each step takes 2-5 minutes, follows TDD order, and requires no re-reading of
  design artifacts at execution time. Use before any multi-step coding task.
---

# Writing Plans

## Goal

Produce a numbered, checkbox-driven `implementation-plan.md` that is concrete
enough to execute mechanically: every file to touch, in the correct layer order,
with verification commands after each layer, and focused TDD cycles embedded
inside execution steps.

**Core principle:** "Write comprehensive plans assuming the engineer has zero
context for our codebase and questionable taste." No placeholders. No vague
directives. Every step must be actionable in 2-5 minutes.

---

## Step 1 — Scope Check

Before planning, verify scope:
- Does this task span multiple independent subsystems? If yes — split into
  separate plans, each producing independently testable software.
- Is the request ambiguous in any dimension that would change the plan? If yes —
  surface the ambiguity before proceeding (use clarifying questions if available).

---

## Step 2 — Load Context

Load what is available (not all sources must exist):

**Requirements / design artifacts:**
- Feature request or task description (from the node instruction)
- `*/questions.md` — answered clarifying questions
- `*/requirements.md`, `*/implementation-plan.md` from prior runs

**Codebase exploration** (always do this):
1. Find build system: `build.gradle`, `pom.xml`, `package.json`, `pyproject.toml`
2. Find entry points and package / module structure
3. Find existing patterns for the layer being changed:
   - Controllers / routes, services / use-cases, entities / models
   - Migration files (Liquibase, Flyway, Alembic)
   - Test examples — unit and integration
4. Find configuration files that may need updating

Collect: **what exists** and **what must be created or modified**.

---

## Step 3 — Identify All Affected Files

For each file:
- **Action**: `Create` or `Modify`
- **Layer**: schema | domain | repository | service | api | test | config | frontend
- **Why**: which requirement or decision drives this change

For `Modify` files: identify the exact method, field, or block — do not plan a
full rewrite unless necessary.

---

## Step 4 — Order Steps by Dependency

Build the execution sequence from foundation to surface:

| Order | Layer | Notes |
|-------|-------|-------|
| 1 | **Schema migrations** | DDL first — tables, columns, indexes |
| 2 | **Domain entities** | Fields, relationships, validations |
| 3 | **Repositories / DAOs** | Interfaces, custom queries |
| 4 | **Services / use-cases** | Business logic, orchestration |
| 5 | **API layer** | Controllers, DTOs, routes |
| 6 | **Tests** | Unit → integration, after each layer |
| 7 | **Configuration** | ENV vars, profiles, feature flags |
| 8 | **Frontend** | After backend API is stable |

**TDD rule:** For each testable behavior, plan a focused local cycle:
write failing test → verify RED → minimal implementation → verify GREEN →
small refactor while green.

Do **not** plan one global RED phase for the whole feature followed by one
global GREEN phase. TDD must happen behavior by behavior inside the execution
plan. Mark test-starting steps with `[TEST-FIRST]`.

**Verification rule:** Add a `Verify:` command after every layer that produces
compilable code. Verification must use only terminating commands (build, compile,
test, lint). Forbidden: `bootRun`, `npm run dev`, browser checks, `curl` to
locally started services.

---

## Step 5 — No-Placeholder Review

Before saving, scan the draft for:
- [ ] Any "TBD", "TODO", or "add error handling" without specifics — replace with exact instructions
- [ ] Any step longer than 5 minutes — split it
- [ ] Any step missing a verification command — add one
- [ ] Any code block in the plan — remove it (descriptions only, no source code)
- [ ] Type inconsistencies across steps (e.g. method returns `String` in step 3, used as `Long` in step 5)

---

## Output Format

Save to `implementation-plan.md` at the path specified in the node instruction.
This is the primary planning artifact for the feature. Do not create a separate
mandatory `tdd-plan.md` unless another workflow explicitly requires it.

```markdown
# Implementation Plan

## Context
- **Task**: [one-sentence summary]
- **Project type**: Brownfield / Greenfield
- **Workspace root**: [path]
- **Build system**: Gradle / Maven / npm / other
- **Scope**: [which layers are touched]

---

## Step 1 — [Layer name] (complexity: low / medium / high)
**Goal**: [what this step achieves and why it comes first]

- [ ] [TEST-FIRST] Create `path/to/SomeTest.java`
  - Behavior: [Given/When/Then description]
  - Expected RED: focused test fails for the correct reason
- [ ] Run focused RED command for `SomeTest`
- [ ] Create / Modify `path/to/File.java`
  - [specific: add field X of type Y | add method foo(Bar bar): Baz]
- [ ] Run focused GREEN command for `SomeTest`
- [ ] Apply small refactor while tests stay green

**Verify**: focused test passes; any broader verification command for the step also passes

---

## Step 2 — [Layer name] (complexity: ...)
...

---

## Full Build Verification
- [ ] `[full build command]` — zero errors, zero test failures
- [ ] `[linter / format check]` if applicable

## Risk Notes
- [Risk]: [what to watch for, potential conflict, rollback consideration]

## Traceability
| Requirement | Steps |
|-------------|-------|
| [req-id]    | 1, 3  |
```

---

## Constraints

- Every step maps to exactly one file action or one shell command.
- File paths must be exact and relative to the workspace root.
- No actual source code in the plan — descriptions and signatures only.
- Brownfield: every file explicitly marked `Create` or `Modify`; never plan copies.
- Do not plan steps for layers with no actual changes.
- The plan must be self-contained — executable without re-reading any other artifact.
- Never use long-running runtime checks as verification.
