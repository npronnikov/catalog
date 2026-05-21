---
name: speckit-analyze
description: >
  Cross-artifact consistency analysis: spec.md ↔ plan.md ↔ tasks.md.
  Detects duplications, ambiguities, requirement coverage gaps, and constitution
  violations. Read-only — never modifies files. Output is analysis-report.md
  with a findings table, coverage metrics, and next-action recommendations.
---

# Cross-Artifact Consistency Analysis

## Purpose

Identify inconsistencies, duplications, ambiguities, and coverage gaps across
the SDD artifacts before implementation begins. Finding a misalignment at this
stage costs minutes; finding it during implementation costs days.

**STRICTLY READ-ONLY**: Do not modify any files. Produce a structured report only.

**Constitution Authority**: The constitution is non-negotiable. Any artifact that
contradicts a constitution MUST principle is automatically a CRITICAL finding.

---

## How to Analyse Artifacts

### Step 1 — Load Context

Read the following files (use only what exists):
- **Required**: `spec.md`, `clarify-questions.md`
- **If exists**: `plan.md`, `tasks.md`, `constitution.md`

If `spec.md` is missing, abort: "Run speckit-specify first."

### Step 2 — Build Semantic Models (Internal Only)

Build internal representations — do not output raw content:
- **Requirements inventory**: For each FR-### and success criterion SC-###, record a key
- **User story inventory**: Discrete user actions with their acceptance criteria
- **Task coverage map**: Map each task to one or more requirements (if tasks.md exists)
- **Constitution rule set**: Extract all MUST/MUST NOT normative statements

### Step 3 — Detection Passes

Run these checks. Limit to 50 findings total; summarise overflow.

#### A. Duplication
- Near-duplicate requirements (same intent, different wording)
- Repeated user stories covering identical scope

#### B. Ambiguity
- Vague adjectives without measurable criteria ("fast", "scalable", "secure", "intuitive")
- Unresolved placeholders (TODO, ???, `<placeholder>`, `[NEEDS CLARIFICATION]` not resolved)

#### C. Underspecification
- Requirements with verbs but missing measurable outcome
- User stories without acceptance criteria
- Tasks referencing files or components not defined in spec or plan

#### D. Constitution Alignment
- Any requirement or plan element contradicting a MUST principle
- Missing mandatory sections or quality gates required by constitution

#### E. Coverage Gaps (when tasks.md exists)
- Requirements with zero associated tasks
- Tasks with no mapped requirement or story
- Success criteria requiring buildable work (performance infra, security tooling) missing from tasks

#### F. Inconsistency
- Terminology drift: same concept named differently across artifacts
- Data entities in plan absent from spec (or vice versa)
- Task ordering contradictions (integration before foundation without dependency note)
- Conflicting requirements

### Step 4 — Assign Severity

| Severity | Criteria |
|----------|---------|
| **CRITICAL** | Violates constitution MUST; missing core artifact; requirement with zero coverage blocking baseline functionality |
| **HIGH** | Duplicate or conflicting requirement; ambiguous security/performance attribute; untestable acceptance criterion |
| **MEDIUM** | Terminology drift; missing non-functional task coverage; underspecified edge case |
| **LOW** | Wording improvements; minor redundancy not affecting execution |

### Step 5 — Write `analysis-report.md`

```markdown
# Specification Analysis Report

**Generated**: YYYY-MM-DD
**Artifacts Analysed**: spec.md, clarify-questions.md[, plan.md, tasks.md, constitution.md]

---

## Findings

| ID | Category | Severity | Location | Summary | Recommendation |
|----|----------|----------|----------|---------|----------------|
| A1 | Ambiguity | HIGH | spec.md FR-003 | "Fast" not quantified | Add specific latency target (e.g., <1s) |
| D1 | Constitution | CRITICAL | spec.md FR-007 | Contradicts library-first principle | Use existing auth library instead |

---

## Coverage Summary

| Requirement | Has Task? | Task IDs | Notes |
|-------------|-----------|----------|-------|
| FR-001 | Yes | T005, T006 | — |
| FR-002 | No | — | Missing task coverage |

---

## Constitution Alignment

[List any MUST violations, or "No violations detected."]

---

## Unmapped Tasks

[Tasks with no linked requirement, or "All tasks mapped."]

---

## Metrics

- Total Requirements: N
- Total Tasks: N (if tasks.md exists)
- Coverage: N% (requirements with ≥1 task)
- Critical Issues: N
- High Issues: N
- Ambiguities: N
- Duplications: N

---

## Next Actions

[If CRITICAL issues]: Resolve before proceeding to implementation.
[If only LOW/MEDIUM]: May proceed; improvements suggested below.

[Specific recommendations for each critical/high finding]
```

---

## Key Rules

- Never modify any file — this is analysis only
- Never hallucinate missing sections: if a section is absent, report it accurately
- Constitution violations are always CRITICAL — do not downgrade them
- If no issues found, emit a success report with coverage statistics
- Report zero issues gracefully: "No critical ambiguities detected. Coverage: 100%."
- When `clarify-questions.md` answers exist, treat them as resolved clarifications
  and update the understanding of spec.md accordingly before running analysis
