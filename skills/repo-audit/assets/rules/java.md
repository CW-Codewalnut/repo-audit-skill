# Java Code Quality Skill

> **Scope:** All `.java` files in any Java project.  
> **Rule ID prefix:** `JV-`  
> Load after `assets/rules/universal.md`. These rules are additive.
> For Spring Boot projects, also load `assets/rules/java-spring-boot.md`.

---

## JV-ORG — Project & Code Organisation

| Rule ID    | Severity | Rule                                                                                                                                                                                   |
| ---------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| JV-ORG-001 | HIGH     | Use standard Spring layer packages: `controller`, `service`, `repository`, `dto`, `model`, `config`, `exception`, `util`.                                                              |
| JV-ORG-002 | HIGH     | Clear separation of concerns — no cross-layer logic bleeding.                                                                                                                          |
| JV-ORG-003 | CRITICAL | No business logic in controllers — delegate everything to services.                                                                                                                    |
| JV-ORG-004 | CRITICAL | No business logic in repositories — only data access.                                                                                                                                  |
| JV-ORG-005 | HIGH     | Services must be stateless — no mutable shared fields.                                                                                                                                 |
| JV-ORG-006 | HIGH     | No business logic in `@Configuration` classes.                                                                                                                                         |
| JV-ORG-007 | MEDIUM   | Follow class structure order: imports → class annotations → constants → injected fields → other fields → constructors → public methods → private/helper methods → inner classes/enums. |

**How to scan:**

- Grep controller classes for non-delegating logic (anything beyond calling a service and returning a response).
- Grep repository interfaces for method implementations containing business logic.
- Grep service classes for `static` mutable fields.

---

## JV-NAME — Naming Conventions

| Rule ID     | Severity | Rule                                                                                                   |
| ----------- | -------- | ------------------------------------------------------------------------------------------------------ |
| JV-NAME-001 | MEDIUM   | Descriptive, meaningful class and method names — no abbreviations unless industry-standard (DTO, API). |
| JV-NAME-002 | HIGH     | No generic names: `data`, `value`, `service1`, `temp`, `misc`.                                         |
| JV-NAME-003 | MEDIUM   | Boolean fields prefixed with `is`, `has`, `can`, `should`.                                             |
| JV-NAME-004 | MEDIUM   | Collection fields must be plural nouns.                                                                |
| JV-NAME-005 | HIGH     | Required class suffixes: `Service`, `Repository`, `Request`, `Response`, `DTO`.                        |
| JV-NAME-006 | MEDIUM   | Constants in `UPPER_SNAKE_CASE`.                                                                       |
| JV-NAME-007 | MEDIUM   | Method names must be verb-based: `createOrder`, `fetchUser`, `validateInput`.                          |
| JV-NAME-008 | MEDIUM   | Exception class names must be descriptive: `OrderNotFoundException`, not `MyException`.                |

**How to scan:**

- Grep all class names for required suffixes in the correct layers.
- Grep `static final` declarations — verify `UPPER_SNAKE_CASE`.
- Grep boolean field declarations — verify `is/has/can/should` prefix.

---

## JV-QUAL — Code Quality & Architecture

| Rule ID     | Severity | Rule                                                                                         |
| ----------- | -------- | -------------------------------------------------------------------------------------------- |
| JV-QUAL-001 | HIGH     | Methods follow single responsibility.                                                        |
| JV-QUAL-002 | MEDIUM   | Methods are small and readable — target ≤ 20 lines.                                          |
| JV-QUAL-003 | MEDIUM   | Prefer early returns over deep nesting.                                                      |
| JV-QUAL-004 | MEDIUM   | Minimise cyclomatic complexity — aim for < 5 per method.                                     |
| JV-QUAL-005 | MEDIUM   | Avoid long parameter lists — use DTOs or request objects when more than 3 parameters needed. |
| JV-QUAL-006 | HIGH     | No duplicated logic — DRY principle.                                                         |
| JV-QUAL-007 | HIGH     | No magic values — use constants or enums.                                                    |
| JV-QUAL-008 | MEDIUM   | Use `final` where variables should not be reassigned.                                        |
| JV-QUAL-009 | MEDIUM   | Define service interfaces + Impl classes when interfaces are used elsewhere.                 |
| JV-QUAL-010 | MEDIUM   | Use Lombok responsibly — not on every class indiscriminately.                                |
| JV-QUAL-011 | HIGH     | No public fields — use accessors.                                                            |
| JV-QUAL-012 | MEDIUM   | Prefer unchecked exceptions for business logic.                                              |
| JV-QUAL-013 | HIGH     | No `@Data` on JPA entities (causes issues with `equals`/`hashCode`/`toString` and proxies).  |
| JV-QUAL-014 | HIGH     | No `@SuppressWarnings` without a justification comment.                                      |

**How to scan:**

- Measure method line counts — flag anything > 20 lines.
- Grep for bare literal strings and numbers used in logic (not in constant declarations).
- Grep for `@Data` on classes also annotated with `@Entity`.
- Grep for `@SuppressWarnings` without a `//` comment on the preceding line.

---

## JV-REST — REST API Standards

| Rule ID     | Severity | Rule                                                                                                                                                                                        |
| ----------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| JV-REST-001 | HIGH     | DTOs used for both request and response — JPA entities never exposed directly from controllers.                                                                                             |
| JV-REST-002 | HIGH     | JPA entities must not be returned from `@RestController` methods.                                                                                                                           |
| JV-REST-003 | HIGH     | Correct HTTP status codes: `200 OK`, `201 Created`, `204 No Content`, `400 Bad Request`, `404 Not Found`, `409 Conflict`, `500 Internal Server Error`, `401 Unauthorized`, `403 Forbidden`. |
| JV-REST-004 | MEDIUM   | Resource-based, lowercase endpoint naming: `/api/v1/orders/{id}`.                                                                                                                           |
| JV-REST-005 | MEDIUM   | API versioning present: `/api/v1/`.                                                                                                                                                         |
| JV-REST-006 | LOW      | No trailing slashes on endpoint paths.                                                                                                                                                      |
| JV-REST-007 | HIGH     | Consistent response structure across all endpoints.                                                                                                                                         |

**How to scan:**

- Grep `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping` method return types — verify they return DTO types, not `@Entity` types.
- Grep `@RequestMapping` and `@*Mapping` path values for versioning and naming consistency.

---

## JV-CLEAN — Clean Coding & Formatting

| Rule ID      | Severity | Rule                                           |
| ------------ | -------- | ---------------------------------------------- |
| JV-CLEAN-001 | MEDIUM   | No commented-out code.                         |
| JV-CLEAN-002 | LOW      | No unused imports.                             |
| JV-CLEAN-003 | MEDIUM   | Consistent formatting throughout the codebase. |

---

## JV-ERR — Error Handling

| Rule ID    | Severity | Rule                                                                                              |
| ---------- | -------- | ------------------------------------------------------------------------------------------------- |
| JV-ERR-001 | CRITICAL | No swallowed exceptions — empty `catch` blocks are always violations.                             |
| JV-ERR-002 | HIGH     | Return a proper error response structure from all error paths.                                    |
| JV-ERR-003 | HIGH     | Use specific exception types — no raw `Exception` or `RuntimeException` without subclassing.      |
| JV-ERR-004 | HIGH     | Validation errors must fail fast and return descriptive messages.                                 |
| JV-ERR-005 | CRITICAL | Error responses must not leak internal details (stack traces, DB messages, internal class names). |
| JV-ERR-006 | HIGH     | Request payload validation must be present for all endpoints that accept input.                   |
| JV-ERR-007 | HIGH     | No validation logic inside controllers — delegate to validators or services.                      |

**How to scan:**

- Grep for `catch (Exception e) { }` and `catch (Exception e) { // ignored }` patterns.
- Grep for `e.printStackTrace()` in production code — this leaks stack traces.
- Grep controllers for `if (request.getField() == null)` validation checks that belong in a validator.

---

## JV-SEC — Security

| Rule ID    | Severity | Rule                                                                              |
| ---------- | -------- | --------------------------------------------------------------------------------- |
| JV-SEC-001 | CRITICAL | Sensitive fields (passwords, tokens, keys) must never appear in log statements.   |
| JV-SEC-002 | CRITICAL | Use environment variables for secrets — never hardcode in source or config files. |
| JV-SEC-003 | CRITICAL | Never hardcode DB credentials or API keys.                                        |

**How to scan:**

- Grep `log.info(`, `log.debug(`, `log.error(`, `System.out.print` for references to `password`, `token`, `secret`, `key`, `credential`.
- Grep `application.properties` and `application.yml` for literal password/secret values (not `${...}` references).
- Grep Java source files for JDBC connection string literals.

---

## JV-DB — Database

| Rule ID   | Severity | Rule                                                                                         |
| --------- | -------- | -------------------------------------------------------------------------------------------- |
| JV-DB-001 | HIGH     | No native queries (`@Query(nativeQuery = true)`) unless explicitly justified with a comment. |
| JV-DB-002 | MEDIUM   | Repository methods must be expressive and readable — use Spring Data naming conventions.     |
| JV-DB-003 | HIGH     | Use JPA standards — no raw SQL for standard CRUD operations.                                 |
| JV-DB-004 | NITPICK  | List endpoints should have pagination support.                                               |
| JV-DB-005 | HIGH     | Avoid N+1 query patterns — use `JOIN FETCH` or `@EntityGraph`.                               |
| JV-DB-006 | MEDIUM   | Consider indexes for query-heavy fields.                                                     |
| JV-DB-007 | LOW      | Use `@Cacheable` where appropriate for stable data.                                          |

**How to scan:**

- Grep for `@Query(nativeQuery = true)` without a comment above it.
- Grep for `findAll()` calls in service code followed by Java-side filtering (sign of missing DB query optimization).
- Grep for lazy-loaded associations accessed in loops (N+1 pattern).

---

## JV-TEST — Test Quality

| Rule ID     | Severity | Rule                                                                    |
| ----------- | -------- | ----------------------------------------------------------------------- |
| JV-TEST-001 | HIGH     | Service-layer unit tests must exist for all new service methods.        |
| JV-TEST-002 | HIGH     | Validation and exception paths must be tested.                          |
| JV-TEST-003 | HIGH     | Follow Arrange–Act–Assert structure in every test.                      |
| JV-TEST-004 | HIGH     | One scenario per test method.                                           |
| JV-TEST-005 | HIGH     | Descriptive test method names: `shouldThrowExceptionWhenOrderNotFound`. |
| JV-TEST-006 | HIGH     | Exception tests must verify message/code, not only exception type.      |
| JV-TEST-007 | HIGH     | Controller tests must be present for all REST endpoints — use MockMVC.  |
| JV-TEST-008 | MEDIUM   | Do not mock simple POJOs or DTOs.                                       |
| JV-TEST-009 | HIGH     | Test behaviour, not implementation internals.                           |
