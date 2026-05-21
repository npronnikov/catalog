---
name: speckit-constitution
description: >
  Creates or updates the project constitution — an immutable governance document
  that defines non-negotiable development principles. Each principle is declarative,
  testable, and carries an explicit rationale. Versioned with semantic versioning.
---

# Project Constitution

## Purpose

The constitution is the source of truth for all project decisions. It defines non-negotiable
principles that every specification, plan, and implementation must comply with.
Downstream artifacts must never contradict the constitution — if there is a conflict,
the artifact must change, not the constitution.

---

## How to Create the Constitution

### Step 1 — Gather Context

Read the feature request and any existing project documentation to understand:
- What kind of project is this? (library, web service, CLI tool, data pipeline, etc.)
- What domain does it operate in? (fintech, healthcare, internal tooling, etc.)
- Are there any regulatory or compliance constraints?
- What is the team's stated technology philosophy?

### Step 2 — Draft the Principles

Create `constitution.md` with the following structure. Every principle must be:
- **Declarative**: States what MUST or MUST NOT be done, not what should be considered
- **Testable**: A reviewer can verify compliance without ambiguity
- **Rationale-backed**: Explains WHY, not just WHAT

**Mandatory principles to include (adapt to project context):**

#### Library-First
Prefer existing, well-maintained libraries over custom implementations. Every new
dependency must justify its addition: Does the existing stack not solve this?
How large is it? Is it actively maintained? Does it have known vulnerabilities?

#### Test-First
No production code without a failing test first. Tests define the contract;
implementation fulfils it. Tests must cover behaviour, not implementation details.

#### Simplicity
Three similar lines are better than a premature abstraction. No half-finished
implementations. No features designed for hypothetical future requirements.
No error handling for scenarios that cannot happen.

#### No Secrets in Code
No credentials, tokens, or secrets in source files, logs, or version control.
Secrets live in environment variables or secret management systems only.

#### Security at Boundaries
Validate and sanitize all user input and external data at system entry points.
Trust internal code; distrust everything external.

### Step 3 — Add Governance Section

Include:
- `version`: semantic version (start at `1.0.0` for new constitutions)
- `ratification_date`: ISO date (YYYY-MM-DD) when first adopted
- `last_amended_date`: ISO date of most recent change
- Amendment procedure: who can propose changes, how they are approved
- Compliance review: when and how the constitution is checked against the codebase

### Step 4 — Version Bump Rules

| Change Type | Version Bump |
|-------------|-------------|
| Principle removed or fundamentally redefined | MAJOR |
| New principle or section added | MINOR |
| Clarification, wording, typo fix | PATCH |

### Step 5 — Validate and Write

Before writing `constitution.md`, verify:
- No vague language ("should" → replace with MUST/SHOULD with explicit rationale)
- No remaining placeholder tokens like `[PROJECT_NAME]`
- All dates in ISO format (YYYY-MM-DD)
- Each principle has: name, rule statement, rationale

Write the completed constitution to `constitution.md`.

---

## Output Format

```markdown
# Project Constitution

**Version**: 1.0.0
**Ratification Date**: YYYY-MM-DD
**Last Amended**: YYYY-MM-DD

---

## Principles

### 1. [Principle Name]

[Declarative rule: MUST / MUST NOT ...]

**Rationale**: [Why this principle exists — the problem it prevents]

### 2. [Principle Name]

...

---

## Governance

### Amendment Procedure
[Who proposes changes, how they are reviewed and ratified]

### Versioning Policy
[MAJOR / MINOR / PATCH criteria]

### Compliance Review
[When and how adherence is checked]
```

---

## Key Rules

- A constitution describes governance, NOT implementation choices
- Principles are immutable within a version — changing them requires a version bump
- If no constitution exists yet, create a minimal one with 3-5 core principles
- If a constitution already exists in `constitution.md`, update it rather than replacing;
  increment the version following the bump rules above
- Every principle that would block or shape implementation decisions MUST be present
- Do not include principles that are aspirational but unenforceable
