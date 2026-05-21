# TypeScript Code Quality Skill

> **Scope:** All `.ts` and `.tsx` files.  
> **Rule ID prefix:** `TS-`  
> Load after `assets/rules/universal.md` and `assets/rules/javascript.md`. These rules are additive.

---

## TS-ANN — Type Annotations

| Rule ID    | Severity | Rule                                                                                        |
| ---------- | -------- | ------------------------------------------------------------------------------------------- |
| TS-ANN-001 | HIGH     | Annotate empty arrays explicitly: `const items: string[] = []` — not `const items = []`.    |
| TS-ANN-002 | HIGH     | Annotate variables initialized to `null` or `undefined`.                                    |
| TS-ANN-003 | MEDIUM   | Use `as const` for literal types, compile-time constants, and configuration objects/arrays. |

**How to scan:**

- Grep for `= []` without a preceding type annotation.
- Grep for `= null` and `= undefined` declarations without a type annotation.

---

## TS-TYPE — Type Usage

| Rule ID     | Severity | Rule                                                                                            |
| ----------- | -------- | ----------------------------------------------------------------------------------------------- |
| TS-TYPE-001 | HIGH     | Avoid `any` — use `unknown` for truly unknown types.                                            |
| TS-TYPE-002 | HIGH     | Prefer `unknown` over `any` in generic constraints.                                             |
| TS-TYPE-003 | HIGH     | Avoid type assertions (`as SomeType`) — prefer type guards.                                     |
| TS-TYPE-004 | CRITICAL | Never use `as any` to silence type errors — fix the types. (Tests are the only exception.)      |
| TS-TYPE-005 | HIGH     | Document every type assertion with a comment explaining why it is necessary.                    |
| TS-TYPE-006 | LOW      | Co-locate types with their implementation — don't scatter them.                                 |
| TS-TYPE-007 | MEDIUM   | Document complex types with JSDoc.                                                              |
| TS-TYPE-008 | MEDIUM   | Use `type` or `interface` consistently within the codebase — don't mix arbitrarily.             |
| TS-TYPE-009 | MEDIUM   | Use discriminated unions (with a common literal property) for type narrowing where appropriate. |
| TS-TYPE-010 | MEDIUM   | Keep types as simple and readable as possible.                                                  |

**How to scan:**

- Grep for `: any` and `as any` throughout the codebase.
- Grep for `as SomeType` type assertions and check for an accompanying justification comment.
- Grep for `any` in generic constraints: `<T extends any>`.

---

## TS-SUP — Suppressions

| Rule ID    | Severity | Rule                                                                               |
| ---------- | -------- | ---------------------------------------------------------------------------------- |
| TS-SUP-001 | HIGH     | Never use `@ts-ignore` — use `@ts-expect-error` with a proper explanation comment. |

**How to scan:**

- Grep for `@ts-ignore` anywhere in the codebase. Every hit is a violation.

---

## TS-STRICT — Strict Compiler Behaviours

These rules apply regardless of the project's `tsconfig.json` settings. If the config is lax, the **code** must still be written as if these are enforced:

| Rule ID       | Severity | Compiler Flag                | What to look for                                            |
| ------------- | -------- | ---------------------------- | ----------------------------------------------------------- |
| TS-STRICT-001 | HIGH     | `strict: true`               | Code must be compatible with strict mode.                   |
| TS-STRICT-002 | HIGH     | `noImplicitAny`              | No untyped parameters or declarations.                      |
| TS-STRICT-003 | HIGH     | `strictNullChecks`           | No unchecked access of potentially null/undefined values.   |
| TS-STRICT-004 | HIGH     | `noImplicitReturns`          | Every code path in every function must return a value.      |
| TS-STRICT-005 | HIGH     | `noFallthroughCasesInSwitch` | Every `switch` case must `break`, `return`, or `throw`.     |
| TS-STRICT-006 | HIGH     | `noUncheckedIndexedAccess`   | Treat array/object index results as `T \| undefined`.       |
| TS-STRICT-007 | MEDIUM   | `exactOptionalPropertyTypes` | Don't assign `undefined` to optional properties explicitly. |

**How to scan:**

- Review every function to ensure all paths return a value.
- Review every `switch` statement for fall-through without a comment.
- Review every array index access (`arr[i]`) for null/undefined guard.

---

## TS-NULL — Null Safety

| Rule ID     | Severity | Rule                                                                                               |
| ----------- | -------- | -------------------------------------------------------------------------------------------------- |
| TS-NULL-001 | HIGH     | Use optional chaining `?.` for safe property access on nullable objects.                           |
| TS-NULL-002 | HIGH     | Avoid non-null assertion `!` unless absolutely certain — document why when used. (Tests excepted.) |
| TS-NULL-003 | HIGH     | Handle `null` and `undefined` explicitly at boundaries: API responses, user input, env vars.       |

**How to scan:**

- Grep for `!` non-null assertions (e.g., `obj!.property`) and verify each one has a justification comment.
- Grep for property access on values that TypeScript would infer as `T | null | undefined` without guarding.

---

## TS-BOUNDARY — Type Safety at Boundaries

| Rule ID      | Severity | Rule                                                                                             |
| ------------ | -------- | ------------------------------------------------------------------------------------------------ |
| TS-BOUND-001 | CRITICAL | Don't trust third-party data — validate at runtime and narrow types at boundaries.               |
| TS-BOUND-002 | HIGH     | Use Zod (or equivalent) for runtime validation at API/data boundaries — not manual conditionals. |

**How to scan:**

- Grep for `as SomeType` applied directly to `JSON.parse(`, `response.json()`, `req.body`, or any external data source — these bypass runtime safety.
- Grep for `fetch(...)` response handling — check that the response body is validated before being typed.
