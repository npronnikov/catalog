---
name: hgdlc-code-review-codex
description: >
  Conducts multi-axis code review across five dimensions: correctness,
  readability, architecture, security, and performance. Use before merging
  any change — whether written by an agent, another model, or a human.
---

# Code Review and Quality

## Overview

Multi-dimensional code review with quality gates. Every change gets reviewed
before merge — no exceptions.

**The approval standard:** Approve a change when it definitely improves overall
code health, even if it isn't perfect. Don't block a change because it isn't
exactly how you would have written it. If it improves the codebase and follows
the project's conventions, approve it.

---

## The Five-Axis Review

### 1. Correctness
- Does the code match the spec or task requirements?
- Are edge cases handled (null, empty, boundary values)?
- Are error paths handled — not just the happy path?
- Do tests actually test the right things?
- Are there off-by-one errors, race conditions, or state inconsistencies?

### 2. Readability & Simplicity
- Are names descriptive and consistent with project conventions? (No `temp`, `data`, `result` without context)
- Is control flow straightforward — no nested ternaries, no deep callbacks?
- Could this be done in fewer lines? (1000 lines where 100 suffice is a failure)
- Are abstractions earning their complexity? (Don't generalize until the third use case)
- Are there dead code artifacts: unused variables, backwards-compat shims, `// removed` comments?

### 3. Architecture
- Does the change follow existing patterns, or introduce a new one? If new, is it justified?
- Are module boundaries clean?
- Is there code duplication that should be shared?
- Are dependencies flowing in the right direction (no circular dependencies)?
- Is the abstraction level appropriate — not over-engineered, not too coupled?

### 4. Security
- Is user input validated and sanitized at system boundaries?
- Are secrets kept out of code, logs, and version control?
- Is authentication / authorization checked where needed?
- Are SQL queries parameterized (no string concatenation)?
- Are outputs encoded to prevent XSS?
- Is data from external sources (APIs, logs, user content, config files) treated as untrusted?

### 5. Performance
- Any N+1 query patterns?
- Any unbounded loops or unconstrained data fetching?
- Any synchronous operations that should be async?
- Any unnecessary re-renders in UI components?
- Any missing pagination on list endpoints?

---

## Review Process

### Step 1 — Understand the Context
- What is this change trying to accomplish?
- What spec or task does it implement?
- What is the expected behaviour change?

### Step 2 — Review the Tests First
- Do tests exist for the change?
- Do they test behaviour (not implementation details)?
- Are edge cases covered?
- Would the tests catch a regression if the code changed?

### Step 3 — Review the Implementation
Walk through each changed file with the five axes in mind:
1. Correctness: Does this code do what the test says it should?
2. Readability: Can I understand this without help?
3. Architecture: Does this fit the system?
4. Security: Any vulnerabilities?
5. Performance: Any bottlenecks?

### Step 4 — Categorize Findings

Label every comment with its severity:

| Prefix | Meaning | Author Action |
|--------|---------|---------------|
| *(no prefix)* | Required change | Must address before merge |
| **Critical:** | Blocks merge | Security vulnerability, data loss, broken functionality |
| **Nit:** | Minor, optional | Author may ignore |
| **Optional:** / **Consider:** | Suggestion | Worth considering but not required |
| **FYI** | Informational only | No action needed |

### Step 5 — Verify the Verification
- What tests were run?
- Did the build pass?
- Were only terminating verification commands used (build / compile / test / lint)?
- Reject verification that depends on starting backend/frontend services,
  browser inspection, or `curl` against locally started endpoints.

---

## Change Sizing

```
~100 lines changed   → Good. Reviewable in one sitting.
~300 lines changed   → Acceptable for a single logical change.
~1000 lines changed  → Too large. Split it.
```

**Splitting strategies:**

| Strategy | When |
|----------|------|
| **Stack** — small change first, next built on top | Sequential dependencies |
| **By file group** — separate changes per reviewer area | Cross-cutting concerns |
| **Horizontal** — shared code / stubs first, then consumers | Layered architecture |
| **Vertical** — smaller full-stack slices of the feature | Feature work |

Separate refactoring from feature work — they are two changes, submit them separately.

---

## Dead Code Hygiene

After any refactoring or implementation:
1. Identify code that is now unreachable or unused.
2. List it explicitly.
3. **Ask before deleting:** "Should I remove these now-unused elements: [list]?"

Do not silently delete things you are not sure about.

---

## Honesty in Review

- **Don't rubber-stamp.** "LGTM" without evidence of review helps no one.
- **Don't soften real issues.** "This might be a minor concern" when it's a production bug is dishonest.
- **Quantify problems when possible.** "This N+1 query will add ~50ms per item" beats "this could be slow."
- **Push back on approaches with clear problems.** Sycophancy is a failure mode in reviews.
- **Accept override gracefully.** If the author has full context and disagrees, defer to their judgment.

---

## Dependency Review

Before adding any dependency:
1. Does the existing stack solve this?
2. How large is it? (Check bundle impact.)
3. Is it actively maintained?
4. Does it have known vulnerabilities? (`npm audit` / `./gradlew dependencyCheckAnalyze`)
5. What's the license?

**Rule:** Prefer standard library and existing utilities over new dependencies. Every dependency is a liability.

---

## Output Format

Save the review report to `code-review-report.md` in the project root (or the path specified in the node instruction). Use the following structure:

```markdown
## Code Review — [change title or node/step name]

### Context
[One sentence: what this change does and what spec it implements]

### Findings

**Critical:**
- [file:line] — [issue description with impact]

**Required:**
- [file:line] — [issue description]

**Nit:**
- [file:line] — [minor suggestion]

**FYI:**
- [observation with no required action]

### Dead Code
- [list of now-unused elements, if any] — safe to remove?

### Verdict
- [ ] **Approve** — ready to proceed
- [ ] **Request changes** — issues above must be addressed first

### Verification
- Tests: [pass / fail / not run]
- Build: [pass / fail / not run]
- Runtime/manual checks: [not required | used incorrectly]
```

---

## Review Checklist

```markdown
### Context
- [ ] I understand what this change does and why

### Correctness
- [ ] Change matches spec / task requirements
- [ ] Edge cases handled
- [ ] Error paths handled
- [ ] Tests cover the change adequately

### Readability
- [ ] Names are clear and consistent
- [ ] Logic is straightforward
- [ ] No unnecessary complexity or dead code

### Architecture
- [ ] Follows existing patterns
- [ ] No unnecessary coupling or circular dependencies
- [ ] Appropriate abstraction level

### Security
- [ ] No secrets in code
- [ ] Input validated at boundaries
- [ ] No injection vulnerabilities
- [ ] Auth checks in place
- [ ] External data treated as untrusted

### Performance
- [ ] No N+1 patterns
- [ ] No unbounded operations
- [ ] Pagination on list endpoints

### Verification
- [ ] Tests pass
- [ ] Build succeeds
- [ ] Only terminating verification commands were used
- [ ] No verification depended on running frontend/backend services or browser checks

### Verdict
- [ ] Approve — ready to merge / proceed
- [ ] Request changes — issues must be addressed
```

---

## Red Flags

- Review that only checks if tests pass (ignoring other axes)
- "LGTM" without evidence of actual review
- Security-sensitive changes without security-focused review
- No regression tests with a bug fix
- Review comments without severity labels
- Accepting "I'll fix it later" — it never happens
