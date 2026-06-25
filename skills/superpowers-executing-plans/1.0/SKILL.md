---
name: superpowers-executing-plans
description: >
  Execute a pre-written implementation plan step by step with checkpoints and
  mandatory stopping rules. Announces progress, tracks state via TodoWrite,
  and halts on blockers rather than guessing. Use after a plan has been approved.
---

# Executing Plans

## Goal

Implement an approved `implementation-plan.md` reliably across one session,
marking each step as it completes and stopping cleanly when blocked.

The plan is the primary artifact. TDD is applied inside the plan's behavior
steps, not as one global RED phase and one global GREEN phase for the feature.

**Core principle:** Follow the plan exactly. Raise blockers immediately — do not
improvise around gaps in the plan, do not skip steps, do not "fix it later."

---

## Step 1 — Announce and Load

At the start of execution, output:
> "Executing plan: [plan name / path]. Loading and reviewing before starting."

Then:
1. Read `implementation-plan.md` fully.
2. Verify the plan is complete — no placeholders, no TBD sections.
3. If the plan has critical gaps that prevent starting, **stop and report** —
   do not guess how to fill them.
4. Create a TodoWrite list mirroring every `[ ]` checkbox in the plan.

---

## Step 2 — Execute Each Step

For each step in the plan, in order:

1. **Mark in_progress** in TodoWrite before touching any file.
2. **Follow the step exactly** as written — the plan has been reviewed and approved.
3. **Run the verification command** specified in the plan's `Verify:` line.
4. **Read the full output** — do not assume success from partial output.
5. **Mark completed** in TodoWrite only after verification passes.
6. **Commit** if the plan includes a `Commit:` line for this step.

When a step starts a TDD behavior cycle:
- run the focused RED command and confirm failure before implementation
- write the minimum code for that same behavior
- run the focused GREEN command and confirm pass
- do only a small refactor before moving to the next behavior

Do not accumulate many failing tests and postpone implementation to a later
global GREEN stage.

**Never skip a step.** If a step seems redundant in hindsight, complete it
anyway — the plan was approved as a unit.

---

## Critical Stopping Points

Stop execution and report clearly when:
- A dependency is missing that the plan assumed was present
- A test fails and three fix attempts have not resolved it
- An instruction is ambiguous enough that two reasonable interpretations give
  different results
- Verification fails repeatedly with no clear cause
- Any step produces output indicating data loss or destructive side effects

When stopping: output the exact blocker, the last successful step number,
and what information is needed to resume.

---

## Step 3 — Complete

When all steps are `[x]`:

1. Run the full build verification from the plan's `Full Build Verification` section.
2. Confirm all tests pass and build is clean.
3. Apply the `superpowers-verification-before-completion` skill before claiming done.
4. Do **not** claim success before fresh verification evidence.

---

## Constraints

- Do not implement features not mentioned in the plan.
- Do not refactor code that is not part of the plan's scope.
- Do not convert the plan into batch-TDD execution (`all RED first`, then
  `all GREEN`) unless the approved plan explicitly says so.
- Do not use long-running runtime processes for verification
  (`bootRun`, `npm run dev`, browser, `curl` to local services).
- Do not create backup copies of files (`File_modified.java`, etc.).
- Do not skip verification commands even if you believe the step is trivially correct.
- Brownfield: modify files in-place; never create copies.
- Do not work on `main` or `master` branch without explicit instruction.

---

## Execution Branch Rule

Before starting, verify you are not on `main` or `master`.
If you are: stop and ask which branch to use.
