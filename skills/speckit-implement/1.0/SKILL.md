---
name: speckit-implement
description: >
  Executes the implementation plan from tasks.md phase by phase, respecting
  task dependencies. Marks each completed task [x] in tasks.md. Parallel tasks
  [P] can be executed independently. Stops on failure and reports context.
  Output: implemented changes in the codebase with all tasks marked [x].
---

# Implementation Execution

## Purpose

Execute every task in `tasks.md` in order, phase by phase, until all tasks are
complete. This skill turns a dependency-ordered task list into working code,
updating the task list after each completion so progress is always visible.

---

## How to Execute Implementation

### Step 1 — Load Context

Read:
- **Required**: `tasks.md` — the complete task list
- **Required**: `plan.md` — tech stack, architecture, file structure
- **If exists**: `spec.md` — original requirements for validation
- **If exists**: `constitution.md` — governance constraints

### Step 2 — Validate Checklists

Before starting, check if any checklist files exist (`checklist.md`).
Count incomplete items (`- [ ]`). If incomplete items exist, report:

```
Checklist status:
- checklist.md: N incomplete items

Proceed with implementation? (yes/no)
```

If the user confirms, proceed. If they decline, halt.

### Step 3 — Parse Task Structure

From `tasks.md`, extract:
- All phases (Setup, Foundation, User Stories, Polish)
- Sequential vs parallel tasks (presence of `[P]` marker)
- Task IDs (T001, T002…) and their dependencies

### Step 4 — Execute Phase by Phase

**For each phase:**

1. Read all tasks in the phase
2. Identify sequential vs parallel groups
3. Execute tasks in order (sequential) or concurrently (parallel `[P]`)
4. After each task: mark it `[x]` in `tasks.md`
5. Run the phase verification command from `plan.md` before moving to the next phase
6. Halt if verification fails — report the error with context

**Sequential task execution:**
- Find the first unchecked task `- [ ]`
- Implement it completely
- Mark it `- [x]` in `tasks.md`
- Move to the next task

**Parallel task execution (`[P]` marked):**
- Group consecutive `[P]` tasks that affect different files
- Implement them in any order (they are independent)
- Mark each `[x]` after completion
- If one fails, continue with others, then report failed ones

### Step 5 — Implementation Rules

**Follow existing patterns:**
- Use the same naming conventions as the rest of the codebase
- Follow existing error handling patterns
- Use existing utilities and helpers — do not reinvent them
- Match existing code style exactly

**Constitution compliance:**
- Library-First: Use existing libraries, do not add new ones without justification
- Test-First: If spec requires tests, write the test before the implementation
- Simplicity: No abstractions unless there are three or more uses
- No Secrets in Code: All credentials via environment variables

**File discipline:**
- Never create copies with suffixes like `_new`, `_modified`, `_backup`
- Modify files in-place
- If a file needs to be replaced, overwrite it directly

### Step 6 — Progress Reporting

After each task completion:
```
✓ T005 [US1] Implemented UserService in src/services/user_service.py
```

After each phase:
```
Phase 3 complete: User Story 1 ✓
Verification: [build/test output]
```

If a task fails:
```
✗ T008 [US1] FAILED: src/models/user.py
Error: [specific error message]
Context: [what was being attempted]
Next steps: [how to resolve]
```

### Step 7 — Final Validation

After all tasks are complete:
1. Verify all tasks in `tasks.md` are marked `[x]`
2. Run the final build and test suite
3. Confirm the implementation matches the original spec requirements
4. Report completion status:

```
Implementation complete.
- Tasks: N/N completed
- Build: ✓ passing
- Tests: ✓ N passing, 0 failing
- Spec compliance: ✓ all requirements addressed
```

---

## Key Rules

- Mark tasks `[x]` IMMEDIATELY after completion — do not batch updates
- Never skip a task — if a task cannot be completed, halt and explain why
- Never modify `tasks.md` structure — only change `[ ]` to `[x]`
- If implementation reveals that a task description is wrong or ambiguous,
  halt and report before proceeding — do not silently reinterpret it
- Each phase must pass its verification checkpoint before the next phase begins
- If the build breaks, fix it before marking the task complete
