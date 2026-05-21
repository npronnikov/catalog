---
name: speckit-checklist
description: >
  Generates a requirements quality checklist — "unit tests for the specification".
  Validates that requirements are complete, clear, consistent, and measurable.
  Does NOT test implementation behaviour. Output is checklist.md with items
  grouped by quality dimension (CHK001…CHKnnn).
---

# Requirements Quality Checklist

## Core Concept: Unit Tests for Requirements

Checklists are **unit tests for requirements writing** — they validate the quality,
clarity, and completeness of what is written in the spec, NOT whether the
implementation works correctly.

**NOT for verification/testing:**
- ❌ "Verify the button clicks correctly"
- ❌ "Test error handling works"
- ❌ "Confirm the API returns 200"

**FOR requirements quality validation:**
- ✅ "Are visual hierarchy requirements defined for all card types?" [Completeness]
- ✅ "Is 'prominent display' quantified with specific sizing/positioning?" [Clarity]
- ✅ "Are hover state requirements consistent across all interactive elements?" [Consistency]
- ✅ "Are accessibility requirements defined for keyboard navigation?" [Coverage]

---

## How to Generate a Checklist

### Step 1 — Load Artifacts

Read `spec.md` and `analysis-report.md` (if exists). Extract:
- Feature domain keywords (auth, latency, UX, API, payments, etc.)
- Risk indicators ("critical", "must", "compliance", "security")
- Ambiguities flagged in analysis-report.md
- User stories and their acceptance criteria

### Step 2 — Generate Items by Quality Dimension

Group checklist items under these categories:

#### Requirement Completeness
Are all necessary requirements documented?
- "Are error handling requirements defined for all failure modes? [Gap]"
- "Are loading state requirements defined for async operations? [Gap]"
- "Are requirements specified for mobile/responsive behaviour? [Gap]"

#### Requirement Clarity
Are requirements specific and unambiguous?
- "Is '[vague term]' quantified with specific criteria? [Clarity, Spec §FR-N]"
- "Are 'related [entity]' selection criteria explicitly defined? [Clarity]"

#### Requirement Consistency
Do requirements align without conflicts?
- "Are [component] requirements consistent between [section A] and [section B]? [Consistency]"
- "Do navigation requirements align across all described user flows? [Consistency]"

#### Acceptance Criteria Quality
Are success criteria measurable and verifiable?
- "Can '[criterion]' be objectively measured without implementation details? [Measurability]"
- "Are all success criteria technology-agnostic? [Measurability]"

#### Scenario Coverage
Are all user flows and cases addressed?
- "Are requirements defined for zero-state scenarios (no data)? [Coverage, Edge Case]"
- "Are concurrent user interaction scenarios addressed? [Coverage, Gap]"
- "Are requirements specified for partial data loading failures? [Coverage]"

#### Edge Case Coverage
Are boundary conditions defined?
- "Are requirements for maximum/minimum value boundaries specified? [Edge Case]"
- "Are race condition or concurrent edit requirements defined? [Edge Case, Gap]"

#### Non-Functional Requirements
Are NFRs specified with measurable targets?
- "Are performance requirements quantified with specific thresholds? [Clarity, NFR]"
- "Are security requirements defined for all sensitive data flows? [Coverage, NFR]"
- "Are availability requirements (uptime, recovery) documented? [Completeness, NFR]"

#### Dependencies & Assumptions
Are they documented and validated?
- "Is the assumption of [external service] availability validated? [Assumption]"
- "Are external API requirements and failure modes documented? [Dependency, Gap]"

### Step 3 — Write `checklist.md`

```markdown
# Requirements Quality Checklist: [Feature Name]

**Purpose**: Validate specification completeness and quality before proceeding to planning
**Generated**: YYYY-MM-DD
**Source**: spec.md

---

## Requirement Completeness

- [ ] CHK001 — Are [requirement type] defined for all [scenario]? [Completeness, Spec §FR-001]
- [ ] CHK002 — Are [edge case] requirements documented? [Gap]

## Requirement Clarity

- [ ] CHK003 — Is '[vague term]' quantified with specific criteria? [Clarity, Spec §FR-003]
- [ ] CHK004 — Are [entity] selection criteria explicitly defined? [Clarity, Spec §FR-005]

## Requirement Consistency

- [ ] CHK005 — Are requirements consistent between [section A] and [section B]? [Consistency]

## Acceptance Criteria Quality

- [ ] CHK006 — Can '[criterion]' be objectively measured? [Measurability, Spec §SC-001]

## Scenario Coverage

- [ ] CHK007 — Are zero-state scenarios addressed in requirements? [Coverage, Edge Case]

## Non-Functional Requirements

- [ ] CHK008 — Are performance targets quantified for critical user journeys? [Clarity, NFR]

## Dependencies & Assumptions

- [ ] CHK009 — Are external dependency failure modes documented? [Dependency, Gap]
```

---

## Item Format Rules

Every item MUST:
- Be a **question** about what is written (or not written) in the spec
- Include a **quality dimension tag**: `[Completeness]`, `[Clarity]`, `[Consistency]`,
  `[Measurability]`, `[Coverage]`, `[Edge Case]`, `[NFR]`, `[Assumption]`, `[Dependency]`, `[Gap]`
- Reference the spec section when checking existing content: `[Spec §FR-N]`
- Use `[Gap]` when checking for missing requirements

**Absolutely prohibited:**
- ❌ Items starting with "Verify", "Test", "Confirm" + implementation behaviour
- ❌ References to code execution or system behaviour
- ❌ "Displays correctly", "works properly", "functions as expected"
- ❌ Implementation details (frameworks, APIs, algorithms)

**Required patterns:**
- ✅ "Are [requirement type] defined/specified/documented for [scenario]?"
- ✅ "Is [vague term] quantified/clarified with specific criteria?"
- ✅ "Are requirements consistent between [section A] and [section B]?"
- ✅ "Can [requirement] be objectively measured/verified?"
- ✅ "Does the spec define [missing aspect]?"

---

## Content Rules

- Soft cap: if raw candidates exceed 40 items, prioritise by risk/impact
- Merge near-duplicates checking the same requirement aspect
- Minimum 80% of items must include a spec section reference or gap/ambiguity marker
- If an ambiguity was flagged in `analysis-report.md`, it must appear as a checklist item
