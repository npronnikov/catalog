---
name: speckit-clarify
description: >
  Scans the feature specification for ambiguities and missing decision points.
  Generates up to 5 targeted clarification questions — each one materially impacts
  architecture, data modeling, or acceptance criteria. Output is clarify-questions.md
  with structured questions and recommended answers.
---

# Specification Clarification

## Purpose

Detect and reduce ambiguity in the feature specification BEFORE technical planning begins.
Every unanswered ambiguity in the spec becomes a hidden assumption in the plan,
which becomes a defect in the implementation.

This skill generates targeted questions — not exhaustive ones. Only ask about
things that would materially change what gets built.

---

## How to Clarify a Specification

### Step 1 — Load and Scan `spec.md`

Perform a structured ambiguity scan across these categories. For each, mark:
`Clear` / `Partial` / `Missing`.

| Category | What to Check |
|----------|--------------|
| **Functional Scope** | Core user goals, explicit out-of-scope, user role distinctions |
| **Domain & Data Model** | Entities, identity rules, lifecycle/state transitions, data volume |
| **UX Flow** | Critical journeys, error/empty/loading states, accessibility |
| **Non-Functional** | Performance targets, reliability, security posture, compliance |
| **Integrations** | External services, failure modes, protocol assumptions |
| **Edge Cases** | Negative scenarios, rate limiting, conflict resolution |
| **Constraints** | Technical constraints, rejected alternatives |
| **Completion Signals** | Testable acceptance criteria, measurable Definition of Done |

### Step 2 — Select Up to 5 Questions

From categories marked `Partial` or `Missing`, generate candidate questions.
Apply these filters — only keep questions where the answer:
- Materially impacts architecture, data modeling, task decomposition, or test design
- Reduces downstream rework risk
- Prevents misaligned acceptance tests

**If more than 5 candidates exist**, rank by `(Impact × Uncertainty)` and keep top 5.

**Never ask about:**
- Things already answered in the spec
- Stylistic preferences with no functional impact
- Plan-level execution details (tech stack choices, library selection)

### Step 3 — Format Questions

For each question, provide a recommended answer with reasoning.

Write the output to `clarify-questions.md`:

```markdown
# Clarification Questions: [Feature Name]

**Generated**: YYYY-MM-DD
**Source**: spec.md

---

## Q1: [Topic / Category]

**Context**: [Quote the relevant spec section that is ambiguous]

**Question**: [Specific question]

**Recommended**: Option [X] — [1-2 sentence reasoning based on best practices]

| Option | Description | Implications |
|--------|-------------|--------------|
| A | [Answer] | [What this means for the feature] |
| B | [Answer] | [What this means for the feature] |
| C | [Answer] | [What this means for the feature] |

**Answer**: _[To be filled by user]_

---

## Q2: [Topic / Category]
...
```

### Step 4 — Coverage Summary

After the questions, include a coverage table:

```markdown
## Coverage Summary

| Category | Status | Notes |
|----------|--------|-------|
| Functional Scope | Clear | — |
| Domain & Data Model | Partial | Q1 addresses this |
| UX Flow | Missing | Q2, Q3 address this |
| Non-Functional | Deferred | Better addressed in planning |
| ... | | |
```

Mark categories as:
- **Resolved** — addressed by a question above
- **Deferred** — low impact or better handled in planning
- **Clear** — already sufficient in spec

---

## Key Rules

- Maximum 5 questions total — quality over quantity
- Each question must have a recommended answer (don't just ask, suggest)
- Questions must be answerable with a short answer (multiple-choice or ≤5 words)
- If no meaningful ambiguities exist, state: "No critical ambiguities detected" and
  include the coverage summary showing all categories as Clear or Deferred
- Do NOT modify `spec.md` — that happens after the user provides answers
- The goal is to reduce rework risk, not to achieve perfect completeness
