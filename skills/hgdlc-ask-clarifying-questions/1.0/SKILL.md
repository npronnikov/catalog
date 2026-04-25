---
name: hgdlc-ask-clarifying-questions
description: >
  Study the project codebase and the user request, then generate 10–15
  targeted clarifying questions with answer options. Each question has a
  recommended option; if left unanswered, the recommended variant is used.
---

# Analyze project and generate clarifying questions

## Goal
Eliminate ambiguity before implementation begins: study the project, formulate
questions whose answers directly affect the choice of solution.

---

## Step 1 — Study the Project

Before formulating questions, explore the codebase:

1. **Entry point**: `README.md`, `build.gradle` / `pom.xml`, `application.yml` / `application.properties`
2. **Package structure**: identify the main layers (api, application, domain, infrastructure)
3. **Domain entities**: key classes, their fields, and relationships
4. **Existing patterns**: how errors, authorization, validation, and transactions are handled
5. **API surface**: list of controllers and their endpoints — what already exists
6. **Test coverage**: whether tests exist and their style (unit, integration, @WebMvcTest)
7. **Configuration**: databases, Liquibase migrations, external integrations

Note for yourself: **what already exists** and **what needs to be changed / added** per the request.

---

## Step 2 — Analyze the Request

Based on the request and the explored codebase, determine:

- Which system components will be affected by the change?
- What is unclear: business rules, edge cases, scope of changes?
- What decisions must be made before implementation starts?
- Where is the highest risk of going wrong due to missing information?

---

## Step 3 — Generate Questions

Formulate **10 to 15 questions** covering the following categories:

| Category | What to clarify |
|----------|----------------|
| **Scope** | What exactly must change, what must not be touched |
| **Business rules** | Validation, constraints, edge cases |
| **API** | Request / response format, HTTP statuses, versioning |
| **Data** | New fields, migrations, backwards compatibility |
| **Integrations** | Dependencies on other services, events, queues |
| **Authorization** | Who can call the new functionality, which roles |
| **Testing** | Expected coverage level, types of tests |
| **Deployment** | Whether a data migration is needed, rollback strategy |

Every question must be **specifically tied** to the project and the request — no abstract questions.

**Rules for generating answer options:**
- Exactly one option in each question is marked as **(recommended)** — the one the agent considers the best default choice.
- If the user does not select any option, the agent uses the recommended one.
- The recommended option must be a realistic and safe default.

---

## Output Format

Save the result to `questions.md`. Strictly follow this format:

```markdown
# Clarifying Questions

## Project Summary
{2–3 sentences: what the project is, its stack, what relevant parts are already implemented}

## Request Analysis
{2–3 sentences: what is being requested, which parts of the project are affected, what the main ambiguity is}

---

**Question 1: {question text}**
> If no option is selected, the recommended variant will be used.
- [ ] Option A: {specific variant}
- [ ] Option B: {specific variant} (recommended)
- [ ] Option C: {specific variant}
- [ ] Other: ___________

**Question 2: {question text}**
> If no option is selected, the recommended variant will be used.
- [ ] Option A: {specific variant} (recommended)
- [ ] Option B: {specific variant}
- [ ] Option C: {specific variant}
- [ ] Other: ___________

... (10–15 questions total)
```

---

## Constraints

- Questions must be strictly on point — no padding questions for the sake of count
- Answer options must be realistic and mutually exclusive
- Every question must have exactly one option marked as `(recommended)`
- Do not propose solutions in questions — only clarify requirements
- Do not write any code
- Output only the content of `questions.md`, nothing else
