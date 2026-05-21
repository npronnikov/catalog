---
name: speckit-tasks
description: >
  Generates a dependency-ordered task list from plan.md and spec.md.
  Tasks are organised by user story, numbered sequentially (T001…),
  marked [P] for parallel execution, and [US1]/[US2] by story.
  Each phase is independently testable. Output is tasks.md ready for
  immediate execution by an AI agent.
---

# Task Decomposition

## Purpose

Break down the implementation plan into a flat, ordered list of tasks that an
AI agent can execute mechanically — one task at a time, in order, without
needing to re-read the spec or plan.

Every task must be:
- **Specific**: Contains an exact file path and action
- **Ordered**: Listed after all its dependencies
- **Independently testable**: Each user-story phase can be verified in isolation

---

## How to Generate Tasks

### Step 1 — Load Artifacts

Read:
- **Required**: `plan.md` (tech stack, architecture, project structure)
- **Required**: `spec.md` (user stories with priorities P1, P2, P3…)
- **If exists**: data model section from `plan.md`
- **If exists**: `clarify-questions.md`

### Step 2 — Extract and Map

1. From `spec.md`: extract user stories sorted by priority (P1 first)
2. From `plan.md`: extract all components to create/modify per phase
3. Map each component to the user story that needs it
4. If a component serves multiple stories, assign it to the earliest story or Phase 1

### Step 3 — Organise by Phase

```
Phase 1 — Setup          (project initialisation, shared infrastructure)
Phase 2 — Foundation     (blocking prerequisites for all user stories)
Phase 3 — User Story 1   (P1 story — all components for US1)
Phase 4 — User Story 2   (P2 story — all components for US2)
…
Final Phase — Polish      (cross-cutting concerns, documentation)
```

Within each user-story phase, order tasks as:
`Models → Repositories → Services → Controllers/Endpoints → Integration`

### Step 4 — Write `tasks.md`

Every task MUST follow this exact format:

```
- [ ] T001 [P] [US1] Description with exact/file/path.ext
```

| Component | Rule |
|-----------|------|
| `- [ ]` | Always start with markdown checkbox |
| `T001` | Sequential ID in execution order |
| `[P]` | Include ONLY if task is parallelisable (different files, no incomplete dependencies) |
| `[US1]` | Include ONLY for user-story phase tasks; omit for Setup/Foundation/Polish |
| Description | Clear action + exact file path |

**Correct examples:**
- `- [ ] T001 Create project structure per implementation plan`
- `- [ ] T005 [P] Implement auth middleware in src/middleware/auth.py`
- `- [ ] T012 [P] [US1] Create User model in src/models/user.py`
- `- [ ] T014 [US1] Implement UserService in src/services/user_service.py`

**Wrong examples:**
- `- [ ] Create User model` — missing ID and story label
- `T001 [US1] Create model` — missing checkbox
- `- [ ] [US1] Create User model` — missing task ID
- `- [ ] T001 [US1] Create model` — missing file path

### Step 5 — Add Dependency Notes

After the task list, include:

```markdown
## Dependencies

- US2 depends on US1 (shared [Entity] model)
- Phase 2 must complete before any user-story phase begins

## Parallel Execution Opportunities

- T005–T008 [P]: All operate on different files, can run simultaneously
- T012–T015 [P] [US1]: Independent model/service files within US1

## Implementation Strategy

- MVP: Implement Phase 3 (US1) only — delivers [core value]
- Full: Complete all phases for complete feature set
```

---

## Key Rules

- Tests are OPTIONAL — generate test tasks only if spec explicitly requests TDD
  or tests are listed as acceptance criteria
- Every task in a user-story phase MUST have its `[US#]` label
- Every task MUST have an exact file path — "create a service" is not acceptable,
  "Create UserService in src/services/user_service.py" is
- Parallel tasks `[P]` MUST operate on different files with no incomplete dependencies
- Each user-story phase must be independently testable — include a verification
  checkpoint at the end of each phase
- The tasks.md must be immediately executable: an agent reads it top-to-bottom
  and implements each unchecked item in order
