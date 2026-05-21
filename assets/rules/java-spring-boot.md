# Java Spring Boot REST API Skill

> **Scope:** Spring Boot REST API projects (non-microservice, single deployable).  
> **Rule ID prefix:** `SR-`  
> Load after `assets/rules/universal.md` and `assets/rules/java.md`. These rules are additive and more specific.

---

## SR-ORG — Project & Code Organisation

| Rule ID    | Severity | Rule                                                                                                                                                                                                                       |
| ---------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SR-ORG-001 | HIGH     | Standard Spring layer packages must exist: `controller`, `service`, `repository`, `dto`, `model`, `config`, `exception`, `util`.                                                                                           |
| SR-ORG-002 | CRITICAL | No circular dependencies between layers — service must not depend on controller; repository must not depend on service.                                                                                                    |
| SR-ORG-003 | HIGH     | Class structure order: imports (standard → Spring → project) → class annotations → fields (constants → injected → others) → constructors → public methods → static methods → private/helper methods → inner classes/enums. |

---

## SR-NAME — Naming Conventions

| Rule ID     | Severity | Rule                                                                                          |
| ----------- | -------- | --------------------------------------------------------------------------------------------- |
| SR-NAME-001 | HIGH     | Required class suffixes: `Controller`, `Service`, `Repository`, `Request`, `Response`, `DTO`. |
| SR-NAME-002 | HIGH     | No `I` prefix on interfaces — `OrderService` not `IOrderService`.                             |
| SR-NAME-003 | MEDIUM   | Package names must be all lowercase with no underscores.                                      |
| SR-NAME-004 | MEDIUM   | Test classes named `<ClassName>Test`: `OrderServiceTest`.                                     |
| SR-NAME-005 | MEDIUM   | Enum types are singular nouns: `OrderStatus` not `OrderStatuses`.                             |

**How to scan:**

- Grep all class declarations for naming compliance.
- Grep interface names for `I` prefix violations.
- Grep package declarations for uppercase letters or underscores.
- Grep test class names for `Test` suffix.

---

## SR-REST — REST API Standards

| Rule ID     | Severity | Rule                                                                                   |
| ----------- | -------- | -------------------------------------------------------------------------------------- |
| SR-REST-001 | CRITICAL | DTOs used for both request and response — JPA entities must never be exposed directly. |
| SR-REST-002 | HIGH     | Resource-based, lowercase endpoint naming: `/api/v1/orders/{id}`.                      |
| SR-REST-003 | HIGH     | Correct HTTP methods: `GET` (read), `POST` (create), `PUT`/`PATCH` (update), `DELETE`. |
| SR-REST-004 | HIGH     | Correct HTTP status codes (see table below).                                           |
| SR-REST-005 | HIGH     | `PUT` = full replacement, `PATCH` = partial update — never interchangeable.            |
| SR-REST-006 | HIGH     | API versioning present on all endpoints: `/api/v1/`.                                   |
| SR-REST-007 | LOW      | No trailing slashes on endpoint paths.                                                 |
| SR-REST-008 | HIGH     | Consistent response structure across all endpoints.                                    |
| SR-REST-009 | HIGH     | No sensitive data in URL paths or query parameters.                                    |

**HTTP Status Code Reference (flag violations):**

| Scenario                    | Required Status             |
| --------------------------- | --------------------------- |
| Successful read             | `200 OK`                    |
| Resource created            | `201 Created`               |
| Delete / no body response   | `204 No Content`            |
| Malformed request           | `400 Bad Request`           |
| Not authenticated           | `401 Unauthorized`          |
| Authenticated but forbidden | `403 Forbidden`             |
| Resource not found          | `404 Not Found`             |
| State conflict              | `409 Conflict`              |
| Semantic validation failure | `422 Unprocessable Entity`  |
| Unexpected server error     | `500 Internal Server Error` |

**How to scan:**

- Grep `@ResponseStatus(HttpStatus.XXX)` and `ResponseEntity.status(XXX)` against the table above.
- Grep `@PostMapping` methods that return `200` instead of `201`.
- Grep `@DeleteMapping` methods that return a body instead of `204`.

---

## SR-ERR — Error Handling

| Rule ID    | Severity | Rule                                                                                                   |
| ---------- | -------- | ------------------------------------------------------------------------------------------------------ |
| SR-ERR-001 | CRITICAL | No swallowed exceptions — no empty `catch` blocks.                                                     |
| SR-ERR-002 | HIGH     | Use specific exception types — never raw `Exception` or `RuntimeException`.                            |
| SR-ERR-003 | HIGH     | Centralized exception handling via `@RestControllerAdvice` — not scattered `try/catch` in controllers. |
| SR-ERR-004 | HIGH     | Consistent error response structure: include `code`, `message`, `timestamp` at minimum.                |
| SR-ERR-005 | HIGH     | Validation errors must return `400` with field-level details.                                          |
| SR-ERR-006 | HIGH     | External system errors must be wrapped in custom exceptions.                                           |
| SR-ERR-007 | CRITICAL | Error responses must not leak internal details (stack traces, DB error messages, class names).         |

**How to scan:**

- Grep for presence of a `@RestControllerAdvice` class — flag if absent.
- Grep for `catch` blocks in `@RestController` classes — these should not exist.
- Grep for `e.printStackTrace()` or returning `e.getMessage()` directly in responses.

---

## SR-VAL — Validation

| Rule ID    | Severity | Rule                                                                                          |
| ---------- | -------- | --------------------------------------------------------------------------------------------- |
| SR-VAL-001 | HIGH     | Request payload validation present on all endpoints that accept input.                        |
| SR-VAL-002 | HIGH     | Use `@Valid` on all controller method parameters accepting DTO input.                         |
| SR-VAL-003 | HIGH     | Use Bean Validation annotations on DTO fields: `@NotNull`, `@NotBlank`, `@Size`, `@Min`, etc. |
| SR-VAL-004 | MEDIUM   | Use custom validators where standard annotations are insufficient.                            |
| SR-VAL-005 | HIGH     | No validation logic inside controllers — delegate to validators or services.                  |
| SR-VAL-006 | HIGH     | Validation errors fail fast and return descriptive messages.                                  |

**How to scan:**

- Grep every `@PostMapping` and `@PutMapping` controller method — verify the DTO parameter has `@Valid`.
- Grep DTO classes used as request bodies — verify they have Bean Validation annotations.
- Grep controller methods for `if (dto.getField() == null)` — these are validation leaks.

---

## SR-SEC — Security

| Rule ID    | Severity | Rule                                                                       |
| ---------- | -------- | -------------------------------------------------------------------------- |
| SR-SEC-001 | CRITICAL | Sensitive fields never logged.                                             |
| SR-SEC-002 | CRITICAL | Secrets and credentials stored in environment variables — never hardcoded. |
| SR-SEC-003 | CRITICAL | No DB credentials or API keys in source code or committed config files.    |
| SR-SEC-004 | HIGH     | Endpoints secured with Spring Security where applicable.                   |
| SR-SEC-005 | HIGH     | Role-based access annotations used: `@PreAuthorize`, `@Secured`.           |
| SR-SEC-006 | CRITICAL | Parameterized queries only — no string concatenation in SQL.               |
| SR-SEC-007 | HIGH     | Rate limiting on public and authentication endpoints.                      |
| SR-SEC-008 | HIGH     | No sensitive data in URL paths or query parameters.                        |

**How to scan:**

- Grep `application.properties` / `application.yml` for literal passwords (not `${ENV_VAR}` references).
- Grep for SQL string concatenation: `"SELECT ... WHERE id = " + id`.
- Grep for missing `@PreAuthorize` on controller methods in secured resources.

---

## SR-PERF — Performance

| Rule ID     | Severity | Rule                                                        |
| ----------- | -------- | ----------------------------------------------------------- |
| SR-PERF-001 | HIGH     | Indexes considered for query-heavy fields.                  |
| SR-PERF-002 | HIGH     | No N+1 query patterns — use `JOIN FETCH` or `@EntityGraph`. |
| SR-PERF-003 | LOW      | Pagination present on list endpoints (`Pageable`).          |
| SR-PERF-004 | LOW      | Caching applied where appropriate (`@Cacheable`).           |
| SR-PERF-005 | LOW      | Async patterns used thoughtfully (`@Async`).                |

---

## SR-DB — Database

| Rule ID   | Severity | Rule                                                                 |
| --------- | -------- | -------------------------------------------------------------------- |
| SR-DB-001 | HIGH     | No native queries unless explicitly justified with a comment.        |
| SR-DB-002 | MEDIUM   | Repository methods must be expressive and follow Spring Data naming. |
| SR-DB-003 | HIGH     | JPA standards followed — no raw SQL for standard CRUD.               |

---

## SR-TEST — Test Quality

| Rule ID     | Severity | Rule                                                                   |
| ----------- | -------- | ---------------------------------------------------------------------- |
| SR-TEST-001 | HIGH     | Service-layer unit tests must exist for all business logic paths.      |
| SR-TEST-002 | HIGH     | Validation and exception paths must be tested.                         |
| SR-TEST-003 | HIGH     | Follow Arrange–Act–Assert in every test.                               |
| SR-TEST-004 | HIGH     | One scenario per test method.                                          |
| SR-TEST-005 | HIGH     | Descriptive test names: `shouldThrowExceptionWhenOrderNotFound`.       |
| SR-TEST-006 | HIGH     | Exception tests must verify `message`/`code`, not only exception type. |
| SR-TEST-007 | HIGH     | Controller tests present for all REST endpoints using MockMVC.         |
| SR-TEST-008 | MEDIUM   | Do not mock simple POJOs or DTOs.                                      |
| SR-TEST-009 | HIGH     | Test behavior, not implementation internals.                           |
| SR-TEST-010 | HIGH     | Test data isolated per test — no shared mutable state between tests.   |
| SR-TEST-011 | MEDIUM   | Prefer constructor-injected mocks over `@MockBean` where possible.     |
