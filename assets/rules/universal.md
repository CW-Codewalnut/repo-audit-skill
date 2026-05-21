# Universal Code Quality Skill

> **Scope:** Every source file in the repository, regardless of language.  
> **Rule ID prefix:** `UNI-`  
> Apply these checks first, before any language-specific skill.

---

## UNI-NAME — Naming

| Rule ID      | Severity | Rule                                                                                                            |
| ------------ | -------- | --------------------------------------------------------------------------------------------------------------- |
| UNI-NAME-001 | MEDIUM   | Names must reveal clear and correct intent. Generic, meaningless names are violations.                          |
| UNI-NAME-002 | MEDIUM   | No single-letter variable names — even in loops, one-liners, or arrow functions.                                |
| UNI-NAME-003 | MEDIUM   | Use the same term for the same concept everywhere. Inconsistency is a violation.                                |
| UNI-NAME-004 | MEDIUM   | Banned variable names: `data`, `info`, `temp`, `misc`, `item`, `value`, `val`, `event` and similar vague words. |
| UNI-NAME-005 | LOW      | Prefer positive conditions: `isActive` not `isNotInactive`.                                                     |
| UNI-NAME-006 | MEDIUM   | Boolean variables/fields must be prefixed: `is`, `has`, `can`, `should`, `was`, `will`.                         |
| UNI-NAME-007 | MEDIUM   | Functions must be named as verbs or verb phrases: `calculateTotal`, `validateInput`.                            |
| UNI-NAME-008 | MEDIUM   | Predicate functions must be named as questions: `isEmpty`, `hasPermission`, `canExecute`.                       |

**How to scan:**

- Grep every identifier declaration for the banned names list.
- Grep every boolean field/variable declaration and check for `is/has/can/should/was/will` prefix.
- Grep every function declaration and check it starts with a verb.

---

## UNI-FUNC — Function Design

| Rule ID      | Severity | Rule                                                                               |
| ------------ | -------- | ---------------------------------------------------------------------------------- |
| UNI-FUNC-001 | HIGH     | Each function must do exactly one thing (Single Responsibility).                   |
| UNI-FUNC-002 | MEDIUM   | Limit parameters to 3 or fewer. Use objects/DTOs for more.                         |
| UNI-FUNC-003 | LOW      | Prefer pure functions (no side effects, no mutation of arguments).                 |
| UNI-FUNC-004 | MEDIUM   | Return early to avoid deep nesting (more than 2 levels of nesting is a violation). |
| UNI-FUNC-005 | MEDIUM   | Avoid optional parameters that drastically change function behavior.               |
| UNI-FUNC-006 | MEDIUM   | Break complex functions into smaller named helpers.                                |
| UNI-FUNC-007 | MEDIUM   | Extract complex boolean conditions into named variables before use.                |

**How to scan:**

- Count parameters in every function/method signature; flag anything > 3.
- Measure nesting depth inside every function; flag anything > 2 levels deep.
- Look for functions longer than 20 lines and flag for SRP review.

---

## UNI-ORG — Code Organisation

| Rule ID     | Severity | Rule                                                                                          |
| ----------- | -------- | --------------------------------------------------------------------------------------------- |
| UNI-ORG-001 | MEDIUM   | One primary concept per file. Files mixing unrelated concerns are violations.                 |
| UNI-ORG-002 | LOW      | Name files after their primary export or purpose.                                             |
| UNI-ORG-003 | MEDIUM   | Organise by feature, not by technical layer (unless the project standard dictates otherwise). |
| UNI-ORG-004 | HIGH     | No module/class that mixes unrelated responsibilities.                                        |

---

## UNI-CMT — Comments & Documentation

| Rule ID     | Severity | Rule                                                                                |
| ----------- | -------- | ----------------------------------------------------------------------------------- |
| UNI-CMT-001 | LOW      | Comment the _why_, not the _what_. Comments that restate the code are noise.        |
| UNI-CMT-002 | MEDIUM   | Public APIs and complex reusable utilities must have JSDoc/Javadoc.                 |
| UNI-CMT-003 | MEDIUM   | Warn about consequences or gotchas in comments.                                     |
| UNI-CMT-004 | MEDIUM   | Do not use comments as a substitute for clear code.                                 |
| UNI-CMT-005 | MEDIUM   | Clarify complex algorithms or formulas with comments.                               |
| UNI-CMT-006 | HIGH     | Explain every linting suppression or best-practice violation inline.                |
| UNI-CMT-007 | MEDIUM   | Keep comments up-to-date with code changes. Stale comments are violations.          |
| UNI-CMT-008 | HIGH     | No commented-out code — unless accompanied by an explanation of why it must remain. |

**How to scan:**

- Search for `//`, `/* */`, `#` comment blocks and review each.
- Search for commented-out code blocks (lines that look like code inside comments).
- Check every `@SuppressWarnings`, `// eslint-disable`, `// @ts-ignore` for an accompanying explanation.

---

## UNI-ANTI — Anti-Patterns

| Rule ID      | Severity | Rule                                                        |
| ------------ | -------- | ----------------------------------------------------------- |
| UNI-ANTI-001 | HIGH     | No magic numbers or strings — extract into named constants. |
| UNI-ANTI-002 | HIGH     | No spaghetti code with tangled, non-linear logic flow.      |
| UNI-ANTI-003 | HIGH     | No copy-paste programming — abstract repeated logic.        |
| UNI-ANTI-004 | MEDIUM   | No "golden hammer" — pick the right tool for the job.       |
| UNI-ANTI-005 | MEDIUM   | Remove unused code — dead code is a violation.              |
| UNI-ANTI-006 | MEDIUM   | Prefer simple solutions over clever or complex ones.        |

**How to scan:**

- Grep for bare numeric literals (other than 0, 1) and string literals used directly in logic.
- Grep for identical code blocks appearing in more than one place (copy-paste detection).
- Grep for identifiers that are declared but never referenced.

---

## UNI-ERR — Error Handling

| Rule ID     | Severity | Rule                                                                                  |
| ----------- | -------- | ------------------------------------------------------------------------------------- |
| UNI-ERR-001 | CRITICAL | Never swallow errors silently (empty catch blocks, errors caught and ignored).        |
| UNI-ERR-002 | HIGH     | Fail fast — detect and report errors as early as possible.                            |
| UNI-ERR-003 | HIGH     | Handle errors at the appropriate layer, not scattered randomly.                       |
| UNI-ERR-004 | HIGH     | Clean up resources in error paths (close connections, cancel requests, free handles). |
| UNI-ERR-005 | HIGH     | Strict defensive programming — validate all assumptions.                              |

**How to scan:**

- Grep for `catch` blocks that are empty or contain only a comment.
- Grep for `catch (e) {}`, `catch (_) {}`, `} catch (Exception e) { }` patterns.

---

## UNI-SEC — Security Basics

| Rule ID     | Severity | Rule                                                                                |
| ----------- | -------- | ----------------------------------------------------------------------------------- |
| UNI-SEC-001 | CRITICAL | Validate all external input: user inputs, API responses, file contents, env vars.   |
| UNI-SEC-002 | CRITICAL | Sanitize data before use — prevent injection attacks (SQL, XSS, command injection). |
| UNI-SEC-003 | CRITICAL | Log security events but **never** log sensitive data (passwords, tokens, PII).      |
| UNI-SEC-004 | CRITICAL | Fail securely — errors must not reveal system internals.                            |
| UNI-SEC-005 | CRITICAL | Secrets and credentials must **never** be hardcoded.                                |

**How to scan:**

- Grep for `password`, `secret`, `api_key`, `apiKey`, `token`, `Bearer` in string literals.
- Grep for `console.log`, `System.out.print`, `log.info` near fields named `password`, `token`, `key`.
- Grep for string concatenation used to build SQL queries.

---

## UNI-TEST — Test Quality

| Rule ID      | Severity | Rule                                                                           |
| ------------ | -------- | ------------------------------------------------------------------------------ |
| UNI-TEST-001 | HIGH     | Treat tests as documentation — they describe how the code behaves.             |
| UNI-TEST-002 | HIGH     | Test behaviour, not implementation details.                                    |
| UNI-TEST-003 | HIGH     | Each test must verify exactly one behaviour.                                   |
| UNI-TEST-004 | MEDIUM   | Use descriptive test names that explain the scenario and expected outcome.     |
| UNI-TEST-005 | MEDIUM   | Follow Arrange–Act–Assert (AAA) structure in every test.                       |
| UNI-TEST-006 | HIGH     | Add unit tests for every unit in isolation.                                    |
| UNI-TEST-007 | HIGH     | Add integration tests for units working together.                              |
| UNI-TEST-008 | HIGH     | Test boundary conditions, nulls, empty collections, and error/exception paths. |
| UNI-TEST-009 | HIGH     | Flaky tests are violations — every test must be deterministic.                 |

---

## Clean Code Principles Reference

These principles underpin all rules above. When a violation is found, reference the relevant principle:

- **DRY** — Don't Repeat Yourself (`UNI-ANTI-003`)
- **AHA** — Avoid Hasty Abstractions (don't over-engineer)
- **SRP** — Single Responsibility Principle (`UNI-FUNC-001`, `UNI-ORG-001`)
- **KISS** — Keep It Simple, Stupid (`UNI-ANTI-006`)
- **YAGNI** — You Aren't Gonna Need It (`UNI-ANTI-005`)
