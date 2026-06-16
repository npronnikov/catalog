---
name: superpowers-verification-before-completion
description: >
  Mandatory gate before any completion claim. Runs fresh verification, reads
  full output, and only then reports success or lists specific rework items.
  "Evidence before claims, always." Use as the final node before human approval.
---

# Verification Before Completion

## Core Principle

**NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE.**

Skipping verification is not efficiency — it is dishonesty. Trusting that
"it should work" or "the previous step passed" is not evidence. Every
completion claim requires a fresh run and a read of the full output.

---

## The Five-Step Gate (mandatory, in order)

1. **Identify** — determine the exact verification commands for this task
2. **Run** — execute each command freshly; do not reuse cached output
3. **Read** — read the complete output including exit code, not just the last line
4. **Verify** — confirm the output actually proves the claim (tests green, build clean, artifacts present)
5. **Report** — only then output `route: on_success` or `route: on_rework`

---

## What to Verify

Check all of the following that apply to this task:

### Plan Completion
- [ ] Every `[ ]` checkbox in `implementation-plan.md` is now `[x]`
- [ ] No step was skipped or partially completed

### Build
- [ ] Full build command passes with zero errors
- [ ] No compilation warnings that indicate logic errors

### Tests
- [ ] All existing tests pass (no regressions)
- [ ] All new tests written in this run pass
- [ ] Test count is consistent with plan (no tests accidentally deleted)

### Artifacts
- [ ] All `produced_artifacts` declared in the flow are present at their declared paths
- [ ] Artifact contents match what was planned (not empty, not stub)

### Code Quality
- [ ] No accidentally committed secrets, credentials, or `.env` files
- [ ] No backup file copies (`_modified`, `_new`, `_old`) left behind
- [ ] No debug code, temporary stubs, or `// TODO` comments introduced

### Scope
- [ ] No changes outside the agreed scope exist in `git diff HEAD`
- [ ] No unrelated files were modified

---

## Prohibited Patterns

These indicate the verification gate is being bypassed:

- Saying "should pass" or "probably works" instead of running the command
- Reading only the last line of test output
- Trusting a previous step's green result without re-running
- Expressing satisfaction before running verification
- Claiming "minor issues only" without listing them explicitly
- Partial checks ("I ran the unit tests" when integration tests also exist)

---

## Output Format

After completing all checks, fill `step-summary` with:

```
route: "on_success" | "on_rework"
actions: [list of verification commands actually run]
issues: [] | [{"severity": "critical|major|minor", "description": "..."}]
rework_instruction: |
  Required only when route="on_rework".
  Numbered list of specific fixes:
  1. path/to/file.ext — what exactly must change and why
  2. ...
```

**route = on_success** only when:
- Build passes
- All tests pass (no regressions, no new failures)
- All plan steps are `[x]`
- No scope violations

**route = on_rework** when any of the above fails. The `rework_instruction`
must name the exact file and the exact fix — not "fix the failing test" but
"fix `SomeTest.java:42` — the assertion expects `HttpStatus.CREATED` but the
controller returns `HttpStatus.OK`."
