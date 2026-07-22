---
name: openspec-verify
description: >
  Verifies implementation against OpenSpec change artifacts across three
  dimensions: completeness, correctness, and coherence. Produces a report.
---

# OpenSpec Verify - Implementation Verification

Check that the implementation matches the change artifacts.

---

## Inputs

The context must include the path to the change:
`openspec/changes/<name>/`.

---

## Steps

### 1. Load all change artifacts

Read:
- `openspec/changes/<name>/proposal.md`
- `openspec/changes/<name>/specs-index.md`
- Spec files listed in `specs-index.md` - all active delta-specs
- `openspec/changes/<name>/design.md` (if it exists)
- `openspec/changes/<name>/tasks.md`

### 2. Initialize report structure

Use three dimensions. Each can contain:
- **CRITICAL** - blocks archiving (unfinished tasks, unimplemented requirements)
- **WARNING** - should be fixed (code differs from spec, uncovered scenarios)
- **SUGGESTION** - improvement (does not match project patterns)

### 3. Dimension 1: Completeness

**Task check:**
- Count `- [x]` (completed) and `- [ ]` (incomplete) in tasks.md
- If any `- [ ]` entries exist, add a CRITICAL item for each incomplete task
- Recommendation: "Complete task: <description>" or "Mark it complete if it is already implemented"

**Spec coverage check:**
- Extract all Requirements from delta-specs (`### Requirement:` sections)
- For each requirement, search the codebase by keywords for an implementation
- If no implementation is found, add a CRITICAL item
- Recommendation: "Implement requirement: <name>"

### 4. Dimension 2: Correctness

**Requirement-to-implementation mapping:**
- For each requirement from delta-specs, find the relevant code
- Assess whether the implementation matches the meaning of the requirement
- If there is a mismatch, add a WARNING with file and line references

**Scenario coverage:**
- For each Scenario (`#### Scenario:` sections), check:
  - The WHEN condition is handled in code
  - The expected THEN result is reached
  - A test covers this scenario
- If a scenario is not covered, add a WARNING

### 5. Dimension 3: Coherence

**Following design.md:**
- If design.md exists, extract key decisions (Decision:, Approach:, Architecture:)
- Check whether the implementation follows those decisions
- If there is a contradiction, add a WARNING: "Decision from design.md is not followed: <decision>"
- Recommendation: update the implementation or update design.md

**Project patterns:**
- Check whether new code follows existing patterns (naming, structure, style)
- Add significant deviations as SUGGESTION items

### 6. Create the report

Save to `verify-report.md`:

```markdown
## Verification: <name>

### Summary

| Dimension | Status |
|---|---|
| Completeness | X/Y tasks, X/Y requirements |
| Correctness | X/Y requirements, X/Y scenarios |
| Coherence | Follows / N deviations |

### Issues

#### CRITICAL (must be fixed before archiving)
- [file:line] - issue description
  -> Recommendation: ...

#### WARNING (should be fixed)
- [file:line] - mismatch description
  -> Recommendation: ...

#### SUGGESTION (improvement)
- [file:line] - description
  -> Recommendation: ...

### Result
```

**Final result line:**

| Situation | Result |
|---|---|
| No issues | `All checks passed. Ready for archiving.` |
| Only warnings/suggestions | `No critical issues. N warnings. Archiving is allowed.` |
| Has CRITICAL | `N critical issues. Must be fixed before archiving.` |

---

## Constraints

- Read only; do not change change artifacts or code
- Do not block archiving on warnings; only CRITICAL blocks it
- Give concrete recommendations: file, line, and exactly what to do
- Do not write vague phrasing such as "maybe consider"; be precise
