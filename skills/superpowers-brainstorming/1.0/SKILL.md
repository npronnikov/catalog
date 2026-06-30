---
name: superpowers-brainstorming
description: >
  Autonomous design analysis before implementation. Explores project context,
  identifies key design decisions, selects the best approach with reasoning,
  and produces a compact brainstorming-summary.md. No human interaction required.
  Use as the first step in a quick development flow before writing-plans.
---

# Autonomous Brainstorming

Turn a feature request into a validated design decision — without waiting for human input.
Explore the codebase, reason through the options, pick the best one, document it.

## Goal

Produce `brainstorming-summary.md`: a compact record of what you understood about the
request, which design decisions mattered, what options existed, and which approach
you selected — with enough reasoning that a human can verify or override any decision
before implementation starts.

---

## Step 1 — Explore Project Context

Before reasoning about the request, load what exists:

1. Entry points: `README.md`, build files (`build.gradle`, `pom.xml`, `package.json`)
2. Package / module structure — identify layers (api, application, domain, infrastructure)
3. Existing patterns: how entities, services, controllers, and tests are structured
4. Migration files if data layer is involved
5. Recent commits or open TODOs related to the area being changed

Note: **what already exists** and **what must be created or modified**.

---

## Step 2 — Understand the Request

From the feature request and codebase context, determine:

- Which system components are affected?
- What is the core behavior being added or changed?
- What are the non-obvious constraints (authorization, validation, backwards compatibility)?
- Where is the highest risk of going wrong without explicit decisions?

List every decision that would change the implementation if answered differently.

---

## Step 3 — Decide, Don't Ask

For each key design decision, consider 2–3 options and **select one**:

- State the options clearly
- Explain why the selected option fits better (project patterns, simplicity, risk)
- Flag if a decision is a guess with low confidence — so the human can override it

Do not leave decisions open. An undecided design is not a design.

**Decision categories to check:**

| Category | What to decide |
|----------|----------------|
| Scope | What exactly changes, what does not |
| Data model | New fields / tables, migration approach |
| API | Endpoint shape, HTTP status codes, request/response format |
| Business rules | Validation, constraints, edge cases — pick concrete behaviour |
| Authorization | Which roles can access the new functionality |
| Testing | What types of tests are appropriate (unit / integration) |
| Error handling | What errors are expected and how they surface |

---

## Step 4 — Self-Review

Before writing the output, scan your decisions for:

- [ ] Any decision still open or marked TBD — make a call
- [ ] Any contradiction between two decisions — resolve it
- [ ] Any scope that would require a second implementation plan — flag for decomposition
- [ ] Any assumption not grounded in the codebase — mark as assumption

Fix inline. Do not leave issues for later.

---

## Output Format

Save to `brainstorming-summary.md`.

```markdown
# Brainstorming Summary

## Feature
[One-sentence description of what is being built]

## Context
- **Project type**: Brownfield / Greenfield
- **Affected layers**: [list]
- **Key existing patterns followed**: [brief note on conventions being matched]

## Key Decisions

### Decision 1: [Topic]
**Options considered**: A / B / C
**Selected**: [Option]
**Reason**: [1–2 sentences grounded in project context]

### Decision 2: [Topic]
...

## Assumptions
- [Any decision made without direct codebase evidence — human should verify]

## Out of Scope
- [Explicitly excluded from this implementation]
```

---

## Constraints

- Do not write any production code or tests.
- Do not create a full spec document — keep the summary compact (one page or less).
- Do not leave decisions open. Every key question must have an answer.
- Decisions should be grounded in existing codebase patterns, not invented from scratch.
- If the request is too large for one implementation plan, flag it clearly and propose decomposition.
