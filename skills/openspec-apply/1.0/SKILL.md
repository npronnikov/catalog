---
name: openspec-apply
description: >
  Implements tasks from tasks.md for the current OpenSpec change, using
  specs/ and design.md as the sources of truth for requirements and decisions.
---

# OpenSpec Apply - Implementation from tasks.md

Implement the tasks from the OpenSpec change.

---

## Inputs

The context must include the path to the active change:
`openspec/changes/<name>/`.

---

## Steps

### 1. Load change context

Read all artifacts in this order:

1. `openspec/changes/<name>/proposal.md` - WHAT is being done and WHY
2. `openspec/changes/<name>/specs-index.md` - the list of active delta-specs for this change
3. Spec files listed in `specs-index.md` - EVERY delta-spec (requirements and scenarios)
4. `openspec/changes/<name>/design.md` - HOW to implement it (decisions and constraints)
5. `openspec/changes/<name>/tasks.md` - the task CHECKLIST

### 2. Show current progress

```markdown
## Implementation: <name>

Tasks: X/N completed
Remaining: [list of incomplete tasks]
```

### 3. Implement tasks in order

For each incomplete task `- [ ]`:

1. Announce: "Working on task T00N: <description>"
2. Implement it minimally and exactly according to the task
3. Check specs whenever behavior is unclear
4. Check design.md whenever a technical choice is unclear
5. After completion, mark it in tasks.md: `- [ ]` -> `- [x]`
6. Move to the next task

**Implementation rules:**
- Keep changes minimal and focused: only what the task requires
- Follow existing codebase patterns
- Do not create duplicate files with suffixes such as `_new`, `_modified`, etc.
- If a task contradicts a spec, report it
- Do not start long-running dev servers, watchers, or background processes for verification
  (`npm run dev`, `vite`, `./gradlew bootRun`, `docker compose up`, etc.) unless the
  current change specification explicitly requires it
- For verification, prefer terminating commands: tests, builds, compilation, linters

### 4. Stop if

- The task is unclear -> ask the user
- Implementation exposes a design/spec problem -> report it and suggest updating the artifact
- A critical error or blocker appears -> describe the situation and wait for guidance
- The user interrupts

### 5. Show the result

```markdown
## Implemented in this session

OK T001 - <description>
OK T002 - <description>
...

Progress: N/M tasks completed

[If all done]: All tasks are complete. Next step: verification.
[If not all done]: Remaining: <list>
```

---

## Output during implementation

```markdown
## Implementation: <name>

Task 3/7: Create migration for table X
[... implementation ...]
OK Task completed

Task 4/7: Add JPA entity X
[... implementation ...]
OK Task completed
```

---

## Constraints

- Implement only what is in tasks.md; do not add "while we are here" work
- Check specs for any unclear behavior
- Use `specs-index.md` as the only list of active delta-specs for the current change
- Check design.md for any unclear technical choice
- If spec and code disagree, this is a problem; report it
- Mark tasks complete immediately after finishing them, not as a batch at the end
- If tasks.md contains a verification task with a long-running process, do not run it
  automatically; record it as a plan problem and prefer a safe build/test check
