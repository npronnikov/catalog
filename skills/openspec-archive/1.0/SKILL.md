---
name: openspec-archive
description: >
  Finalizes an OpenSpec change: merges delta-specs into openspec/specs/
  (documentation update), then archives the change directory.
---

# OpenSpec Archive - Archive and Documentation Update

This is the final step of the OpenSpec cycle. It does two things:
1. **Updates documentation**: merges delta-specs into the main `openspec/specs/`
2. **Archives the change**: moves the directory to `openspec/changes/archive/`

---

## Inputs

The context must include the path to the change:
`openspec/changes/<name>/`.

---

## Steps

### 1. Check artifact state

Read `openspec/changes/<name>/tasks.md`.

Count incomplete tasks `- [ ]`.

If there are incomplete tasks, report:

```text
WARNING: N incomplete tasks in tasks.md.
Implementation should be completed before archiving.
```

Do not block; continue if the user confirmed.

### 2. Synchronize delta-specs -> main specs

This is the key step: **documentation update**.

Read `openspec/changes/<name>/specs-index.md` and use it as the only list of
active delta-specs for the current change.

For each file listed in `specs-index.md`:

**a. Determine the operation:**

| Situation | Action |
|---|---|
| `openspec/specs/<capability>/` does not exist | Create the new capability completely |
| Existing capability, delta contains `### ADDED Requirements` | Add new sections |
| Existing capability, delta contains `### MODIFIED Requirements` | Update the corresponding sections |
| Existing capability, delta contains `### REMOVED Requirements` | Remove the corresponding sections |

**b. Apply changes to `openspec/specs/<capability>/spec.md`:**

- When adding, insert new Requirements in the logically appropriate place
- When modifying, replace only the specified Requirements; leave the rest untouched
- When removing, remove only the specified Requirements
- Preserve formatting and the structure of the rest of the file

**c. If the spec does not exist, create it from scratch:**

```markdown
# <Capability> Specification

## Purpose
[Carry over the purpose from the delta]

## Requirements
[Carry over all Requirements from the delta, removing ADDED/MODIFIED/REMOVED markers]
```

**d. Show progress:**

```text
OK Synchronized: openspec/specs/<capability>/spec.md
  + Added requirements: N
  ~ Modified requirements: M
  - Removed requirements: K
```

### 3. Archive the change directory

**Generate the target name:**

```text
openspec/changes/archive/YYYY-MM-DD-<name>/
```

where the date is current.

**Create the archive directory if it does not exist:**

```text
openspec/changes/archive/
```

**Check that the target directory does not already exist.** If it exists, report
an error.

**Rename/move:**
Move `openspec/changes/<name>/` to
`openspec/changes/archive/YYYY-MM-DD-<name>/`.

All files (proposal.md, design.md, tasks.md, specs/, .openspec.yaml if present)
move together.

### 4. Show the result

```markdown
## Archive complete

**Change:** <name>

**Documentation update:**
OK openspec/specs/auth/spec.md - added 3 requirements
OK openspec/specs/session/spec.md - modified 1 requirement

**Archive:** openspec/changes/archive/YYYY-MM-DD-<name>/

Documentation is up to date. Change is complete.
```

---

## Constraints

- Never delete main `openspec/specs/<cap>/spec.md`; only update it
- During merge, keep all unrelated spec content untouched
- If a conflict appears and the integration point is unclear, report it instead of guessing
- Move the whole directory, do not copy it; artifacts should live in archive/
