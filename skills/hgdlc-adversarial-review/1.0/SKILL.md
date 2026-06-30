---
name: hgdlc-adversarial-review
description: >
  Adversarial code review using three parallel layers: Blind Hunter (general bugs),
  Edge Case Hunter (boundary conditions), Context Auditor (project conventions from
  agent instruction files). Structured triage into patch/decision/defer buckets with
  optional auto-fix. Use when the user says "сделай ревью", "code review", or "проверь код".
---

# Adversarial Code Review

**Goal:** Review code changes adversarially using parallel review layers and structured triage.

**Your Role:** Elite code reviewer. Gather context, launch parallel adversarial reviews, triage findings with precision, present actionable results. No noise, no filler.

---

## On Activation

### Load Project Context

Before anything else, load project conventions into `{project_context}`. Check the project root for agent instruction files in this order — load the first one found, or load all that exist:

| File | Agent |
|------|-------|
| `CLAUDE.md` | Claude Code |
| `AGENTS.md` | Codex / generic agents |
| `GEMINI.md` | Gemini |
| `QWEN.md` | Qwen |
| `GIGACODE.md` | GigaCode |
| `.cursorrules` | Cursor |

If none of these exist, check for any `README.md` or `docs/` directory and extract conventions from there.

Also load:
- Any memory or context files referenced in the conversation
- Current date (system-generated)

Treat everything loaded as `{project_context}` — foundational facts carried for the whole review run.

---

## Phase 1 — Gather Context

### 1.1 Find the review target

Check in this order — stop as soon as the review target is identified:

**Tier 1 — Explicit argument in the trigger message.**
- PR number → `gh pr view <N>` to resolve branch/commit
- Branch name → branch diff against the default branch
- Commit SHA or range → use directly
- Keyword "staged" → staged changes only
- Keyword "uncommitted" / "working tree" → staged + unstaged

**Tier 2 — Recent conversation.** Do the last few messages reveal what to review?

**Tier 3 — Current git state.** If the current branch is not `main` (or the default branch), confirm:
> "Вижу ветку `<branch>` — сделать ревью изменений этой ветки против `main`?"
If confirmed, treat as a branch diff. If declined, fall through.

**Tier 4 — Ask.**
> Что ревьюим?
> 1. Незакоммиченные изменения (`git diff HEAD`)
> 2. Только staged (`git diff --cached`)
> 3. Diff ветки против main
> 4. Конкретный диапазон коммитов
> 5. PR номер

### 1.2 Build `{diff_output}`

- **staged:** `git diff --cached`
- **uncommitted:** `git diff HEAD`
- **branch diff:** verify base branch exists, then `git diff <base>...HEAD`
- **commit range:** verify range resolves, then `git diff <from>..<to>`
- **PR:** `gh pr diff <N>`

Verify `{diff_output}` is non-empty. If empty — HALT and tell the user there is nothing to review.

If `{diff_output}` exceeds ~2000 lines, warn the user and offer to chunk by file group.
- If the user opts to chunk: agree on the first group, narrow `{diff_output}` accordingly, list remaining groups for follow-up.
- If declined: proceed with the full diff.

### 1.3 Checkpoint

Present a summary: files changed, lines added/removed. HALT and wait for user confirmation before proceeding.

---

## Phase 2 — Parallel Review

Launch three review layers in parallel as subagents. If subagents are unavailable, run them sequentially in the current session and note it.

Record any failed or empty layer in `{failed_layers}`.

---

### Layer A — Blind Hunter

**Context given:** diff only — no project context.

**Prompt:**
> You are a cynical, jaded senior engineer with zero patience for sloppy code. Review the following diff adversarially — assume problems exist, find at least 10.
>
> Look for: bugs, logic errors, null/empty risks, incorrect error handling, missing validation, security issues (injection, auth bypass, secrets in code), performance problems (N+1 queries, unbounded loops), dead code, incorrect use of APIs or frameworks, missing tests for changed behaviour.
>
> Output: Markdown list of descriptions only. No severity labels, no ranking.
>
> ```diff
> {diff_output}
> ```

---

### Layer B — Edge Case Hunter

**Context given:** diff only.

**Prompt:**
> You are an exhaustive edge-case path tracer. Walk every branching path and boundary condition in the diff. Report ONLY unhandled edge cases — no editorializing, no severity labels.
>
> Method: mechanically enumerate every branch (control flow, domain boundaries, value transitions). For each path, determine whether the diff handles it. Collect only unhandled paths — discard handled ones silently.
>
> Output: JSON array only, no extra text.
> ```json
> [{
>   "location": "file:line (or file:hunk if exact line unknown)",
>   "trigger_condition": "one-line description (max 15 words)",
>   "guard_snippet": "minimal code that closes the gap (single escaped line)",
>   "potential_consequence": "what could go wrong (max 15 words)"
> }]
> ```
>
> Empty array `[]` is valid if no unhandled paths found.
>
> ```diff
> {diff_output}
> ```

---

### Layer C — Context Auditor

**Context given:** diff + `{project_context}`.

**Prompt:**
> You are a project conventions auditor. Review the diff for violations of the rules and constraints described below, extracted from the project's documentation.
>
> Project conventions:
> {project_context}
>
> Check for: violations of architecture layer rules, missing required file registrations (e.g. migration changelogs), incorrect exception or error handling patterns, wrong abstraction placement, naming convention violations, deviations from documented API/framework usage patterns.
>
> Output: Markdown list. Each finding: which rule/convention is violated, file:line, one-line explanation.
>
> ```diff
> {diff_output}
> ```

---

## Phase 3 — Triage

### 3.1 Normalize

Convert all findings to a unified list:
- `id` — sequential integer
- `source` — `blind`, `edge`, `context`, or merged (e.g. `blind+edge`)
- `title` — one-line summary
- `detail` — full description
- `location` — file:line if available

For Edge Case Hunter output: parse JSON, map fields to the unified format.
If a layer's output doesn't match its expected format, attempt best-effort parsing and note any parsing issues.

### 3.2 Deduplicate

Merge findings describing the same issue. Prefer the most specific finding as the base (edge JSON with location > adversarial prose). Set `source` to merged sources.

### 3.3 Assign severity

Severity by consequence:
- `low` — cosmetic or no user impact
- `medium` — tolerable but should be fixed
- `high` — breaks behavior, data loss, security issue, or hard constraint violation

Disregard any severity assigned by a reviewing layer — they operate with intentional information asymmetry and cannot set final severity.

### 3.4 Route

Route each finding to exactly one bucket:
- **patch** — fixable without human input; correct fix is unambiguous
- **decision** — ambiguous fix requiring human choice
- **defer** — pre-existing issue not caused by this change
- **dismiss** — false positive or noise

Drop all `dismiss` findings; record the dismiss count for the summary.

If `{failed_layers}` is non-empty and zero findings remain after triage, warn the user the review may be incomplete rather than announcing a clean result.

If zero findings remain: `✅ Чистое ревью — все слои прошли.`

---

## Phase 4 — Present and Act

### 4.1 Summary

Report:
```
Ревью завершено.
  decision: <D>
  patch:    <P>
  defer:    <W>
  dismissed: <R>
```

List all non-dismissed findings grouped by bucket, then by severity (high → medium → low).

If `{failed_layers}` is non-empty, report which layers failed before the findings.

### 4.2 Resolve `decision` findings

If `decision` findings exist, present each with its detail and available options. The correct fix is ambiguous without the user's input — walk through each (or batch related ones) and get their call.

Each resolved finding becomes `patch`, `defer`, or is dismissed.

**HALT** after presenting — wait for user input before proceeding.

### 4.3 Handle `patch` findings

If `patch` findings exist (including any resolved from 4.2), HALT. Ask:

> **Как обработать `<P>` patch-находок?**
> 1. Применить все сейчас — без подтверждения по каждой
> 2. Пройтись по каждой — показать детали и предложить фикс
> 3. Оставить как есть

- **Option 1:** Apply all patches without per-finding confirmation. Present a summary of changes made.
- **Option 2:** Show each finding with full detail, diff context, and suggested fix. Re-offer options 1/3 after walkthrough.
- **Option 3:** Done — findings listed above, no changes made.

**HALT** — жду выбор. Не продолжать без ответа.

### 4.4 Save report

Write the full review report to `review-report.md` in the project root. Format:

```markdown
## Code Review — <branch or commit ref> (<date>)

### Summary
- decision: <D>
- patch:    <P>
- defer:    <W>
- dismissed: <R>
- Failed layers: <{failed_layers} or "none">

### Findings

#### Decision needed
- [<id>] **<title>** (`<location>`) — <detail>

#### Patch
- [<id>] **<title>** (`<location>`) — <detail>

#### Deferred
- [<id>] **<title>** (`<location>`) — <detail> *(pre-existing)*

### Applied fixes
- <list of files changed, or "none">
```

Omit sections with zero findings. After writing, confirm the path to the user.

### 4.5 Final summary

```
✅ Ревью завершено.
  Исправлено:          <fixed>
  Отложено:            <deferred>
  Оставлено как есть:  <skipped>
  Отклонено (noise):   <dismissed>

Отчёт сохранён: review-report.md
```
