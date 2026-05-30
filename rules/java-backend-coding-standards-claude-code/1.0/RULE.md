---
id: java-backend-coding-standards-claude-code
version: "1.0"
canonical_name: java-backend-coding-standards-claude-code@1.0
title: Java Backend Coding Standards (Claude Code)
description: >
  Core coding rules for Java/Spring Boot backend development:
  project structure, layering, naming, error handling, transactions,
  testing, and API design.
allowed_paths:
  - backend/src/main/java
  - backend/src/main/resources
  - backend/src/test
  - src/main/java
  - src/main/resources
  - src/test
forbidden_paths:
  - frontend/src
  - .github
allowed_commands:
  - ./gradlew test
  - ./gradlew build
  - mvn test
  - mvn package
require_structured_response: true
---

# Java Backend Coding Standards

## Purpose

Enforce consistent structure, naming, error handling, and testing conventions
across all Java/Spring Boot backend code. Apply this rule to every task that
creates or modifies Java sources, resources, or migrations.

---

## Mandatory requirements

- Package structure must follow hexagonal layering: `domain` → `application` → `adapters`
- `domain` must not import Spring, Jackson, JPA, or any `application`/`adapters` class
- `application` must not import `jakarta.persistence`, `spring.web`, or `adapters` classes
- Every new public service method must have at least 2 tests (success + failure)
- All schema changes go through Liquibase changesets — never use `ddl-auto: create/update`
- All JSON fields in `snake_case`
- All exceptions must be typed (`NotFoundException`, `ValidationException`, `ConflictException`, `ForbiddenException`) — never throw raw `RuntimeException`

---

## Architecture conventions

**Package layout per module:**

```
ru.{company}.{service}.
  domain/        — JPA entities, value objects (no Spring/Jackson)
  application/   — use-cases, ports (port/in, port/out), commands, results
  adapters/
    rest/        — REST controllers, request/response records
    persistence/ — JPA repositories, persistence adapters
    plugin/      — external integrations
```

**Dependency rules:**
- `adapters.rest` must not depend on `adapters.persistence`
- Controllers call only application-layer ports (never repositories directly)
- Transactions belong on service layer only — never on controllers or repositories

**Naming:**

| Element | Convention | Example |
|---------|------------|---------|
| Controller | `{Domain}Controller` | `OrderController` |
| Service | `{Domain}Service` | `OrderService` |
| Repository interface | `{Domain}Repository` | `OrderRepository` |
| JPA entity | `{Domain}` or `{Domain}Entity` | `Order` |
| Request DTO (record) | `{Action}Request` | `CreateOrderRequest` |
| Response DTO (record) | `{Domain}Response` | `OrderResponse` |
| Command | `{Action}Command` | `CreateOrderCommand` |
| Port interface | `{Domain}Port` | `OrderNotificationPort` |

---

## Code change rules

**REST controllers:**
- URLs in `kebab-case`, plural nouns: `/api/orders/{orderId}/items`
- Return `ResponseEntity<T>` always
- Status codes: `200 OK`, `201 Created`, `204 No Content`, `400`, `404`, `409`
- Use `@Valid` on `@RequestBody` parameters
- Response/request types as `record` defined inside the controller class

**Service layer:**
- `@Transactional(readOnly = true)` as class-level default
- Override with `@Transactional` on mutating methods
- Business validation throws `ValidationException`; missing entity throws `NotFoundException`

**Java idioms:**
- Use `record` for DTOs, commands, value objects
- Prefer `Optional<T>` over returning `null`
- Use `var` for local variables when type is obvious from the right-hand side
- Use Stream API for collection transformations instead of `for` loops

**Liquibase migrations:**
- File name: `NNN-description.sql` (sequential number)
- Register every new file in `db/changelog/db.changelog-master.yaml`
- Tables and columns in `snake_case`
- Always add indexes on foreign keys and frequently filtered columns

**Testing:**
- Test method name format: `{method}_{condition}_{expectedResult}`
- Mock only external dependencies (repositories, HTTP clients), not business logic
- Use `@ParameterizedTest` for boundary cases

---

## What not to do

- Do not import Spring/JPA/Jackson in `domain` classes
- Do not place `@Transactional` on controllers or repositories
- Do not throw `RuntimeException` directly — use typed exceptions
- Do not call `@Transactional` methods from within the same Spring bean (self-invocation)
- Do not log and rethrow the same exception (double logging)
- Do not log passwords, tokens, or PII data
- Do not delete or rename columns in the same changeset as adding new ones
- Do not touch `frontend/src` from a backend task
- Do not modify `.github` CI/CD config unless explicitly asked

---

## Report format

After completing the task, run:

```
./gradlew test
```

Report:
1. List of files created or modified
2. Test results: passed / failed count
3. Any new compiler warnings introduced
4. Confirmation that package structure follows the conventions above
