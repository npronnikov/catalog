---
name: speckit-specify
description: >
  Creates a structured feature specification from a natural language description.
  Technology-agnostic: focuses on WHAT users need and WHY, never HOW to implement.
  Output is spec.md with user stories, functional requirements, success criteria,
  and edge cases — ready for clarification and planning.
---

# Feature Specification

## Purpose

Transform a natural language feature description into a structured, unambiguous specification
that can be handed to a technical planner without further conversation. The spec answers:
- What do users need to accomplish?
- Who are the actors?
- What are the measurable success criteria?
- What are the known edge cases and constraints?

The spec does NOT contain: technology choices, framework names, database types, API designs,
or any other implementation detail.

---

## How to Create the Specification

### Step 1 — Parse the Feature Description

Extract from the feature description:
- **Actors**: Who uses this feature? (user, admin, system, external service)
- **Actions**: What do they need to do?
- **Data**: What information is involved?
- **Constraints**: What rules or limits apply?

### Step 2 — Make Informed Guesses, Limit Clarifications

Fill in gaps using industry standards and common patterns. Only mark
`[NEEDS CLARIFICATION: specific question]` when:
- The choice significantly impacts feature scope or user experience
- Multiple reasonable interpretations exist with materially different implications
- No reasonable default exists

**Maximum 3 `[NEEDS CLARIFICATION]` markers total.** Prioritize by impact:
scope > security/privacy > user experience > technical details.

**Examples of reasonable defaults (do not ask about these):**
- Data retention: industry-standard practices for the domain
- Performance targets: standard web/mobile app expectations
- Error handling: user-friendly messages with appropriate fallbacks
- Authentication method: standard session-based or OAuth2 for web apps

### Step 3 — Write the Specification

Create `spec.md` with this structure:

```markdown
# Feature Specification: [Feature Name]

**Created**: YYYY-MM-DD
**Status**: Draft

---

## Overview

[1-3 sentence summary: what the feature does and why it matters to users]

## Actors

- **[Actor Name]**: [What role they play and their key permissions/constraints]

## User Stories

### US1: [Story Name] (Priority: P1)
As a [actor], I want to [action] so that [benefit].

**Acceptance Criteria:**
- [ ] [Measurable, testable criterion]
- [ ] [Measurable, testable criterion]

### US2: [Story Name] (Priority: P2)
...

## Functional Requirements

### FR-001: [Requirement Name]
[Clear, testable statement of what the system must do]

### FR-002: [Requirement Name]
...

## Success Criteria

### Measurable Outcomes
- [Quantitative metric: e.g., "Users complete checkout in under 3 minutes"]
- [Quantitative metric: e.g., "System supports 10,000 concurrent users"]

### Out of Scope
- [Explicitly excluded functionality]

## Edge Cases & Error Handling

- **[Scenario]**: [Expected behaviour]
- **[Scenario]**: [Expected behaviour]

## Assumptions

- [Documented assumption about context or defaults used]

## Dependencies

- [External dependency or prerequisite]
```

### Step 4 — Validate the Specification

After writing the initial spec, self-validate against these criteria:

**Content Quality**
- [ ] No implementation details (languages, frameworks, APIs, databases)
- [ ] Focused on user value and business needs
- [ ] Understandable by non-technical stakeholders
- [ ] All mandatory sections completed

**Requirement Completeness**
- [ ] No more than 3 `[NEEDS CLARIFICATION]` markers remain
- [ ] Requirements are testable and unambiguous
- [ ] Success criteria are measurable (include specific numbers/rates/times)
- [ ] Success criteria are technology-agnostic
- [ ] All acceptance scenarios are defined
- [ ] Edge cases are identified
- [ ] Scope is clearly bounded

If items fail, update the spec to address them before finalising.

### Step 5 — Handle Clarification Markers

If `[NEEDS CLARIFICATION]` markers remain, present them as structured questions
with suggested options before writing the final spec.

---

## Success Criteria Guidelines

Success criteria MUST be:
1. **Measurable** — specific metrics (time, percentage, count, rate)
2. **Technology-agnostic** — no frameworks, languages, or tools mentioned
3. **User-focused** — outcomes from user/business perspective
4. **Verifiable** — can be tested without knowing implementation details

**Good examples:**
- "Users can complete checkout in under 3 minutes"
- "System supports 10,000 concurrent users"
- "95% of searches return results in under 1 second"

**Bad examples:**
- "API response time is under 200ms" (too technical)
- "Database can handle 1000 TPS" (implementation detail)
- "React components render efficiently" (framework-specific)

---

## Key Rules

- Write for a business stakeholder, not a developer
- Every requirement must be independently testable
- Vague adjectives ("fast", "robust", "intuitive") always need quantification
- If a section does not apply to this feature, omit it entirely — do not write "N/A"
- Document every assumption made when filling in gaps
