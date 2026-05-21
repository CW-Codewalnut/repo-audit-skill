# JavaScript Code Quality Skill

> **Scope:** All `.js` and `.jsx` files.  
> **Rule ID prefix:** `JS-`  
> Load after `assets/rules/universal.md`. These rules are additive.

---

## JS-NAME — JavaScript Naming Conventions

| Rule ID     | Severity | Rule                                                                         |
| ----------- | -------- | ---------------------------------------------------------------------------- |
| JS-NAME-001 | MEDIUM   | Use `camelCase` for variables, functions, and method names.                  |
| JS-NAME-002 | MEDIUM   | Use `PascalCase` for classes and constructor functions.                      |
| JS-NAME-003 | MEDIUM   | Use `SCREAMING_SNAKE_CASE` for constants and environment variables.          |
| JS-NAME-004 | NITPICK  | Name event handlers with `handle` prefix: `handleClick`, `handleSubmit`.     |
| JS-NAME-005 | NITPICK  | Name event handler props/callbacks with `on` prefix: `onClick`, `onSuccess`. |

**How to scan:**

- Grep every `const`, `let`, `var`, `function` declaration and verify casing.
- Grep `class` declarations for `PascalCase`.
- Grep top-level `const` declarations for `SCREAMING_SNAKE_CASE` where the value is a literal.

---

## JS-FUNC — Function Design

| Rule ID     | Severity | Rule                                                                         |
| ----------- | -------- | ---------------------------------------------------------------------------- |
| JS-FUNC-001 | HIGH     | No magic numbers or strings — extract into named constants.                  |
| JS-FUNC-002 | HIGH     | Each function must do one thing and do it well.                              |
| JS-FUNC-003 | MEDIUM   | Limit function parameters to 3; use an options object for more.              |
| JS-FUNC-004 | HIGH     | Never mutate function arguments — return new values instead.                 |
| JS-FUNC-005 | MEDIUM   | Choose the simplest, most readable solution; avoid clever or unusual syntax. |

**How to scan:**

- Grep for direct mutation of parameters: `param.x =`, `param.push(`, `Object.assign(param`.
- Count parameters in every function signature.

---

## JS-ORG — File Organisation

| Rule ID    | Severity | Rule                                                                                                     |
| ---------- | -------- | -------------------------------------------------------------------------------------------------------- |
| JS-ORG-001 | MEDIUM   | Files and folder conventions must be consistent within the codebase.                                     |
| JS-ORG-002 | NITPICK  | One primary export per file (components, services, orchestrators). Small utilities may group.            |
| JS-ORG-003 | NITPICK  | Types and utilities scoped to their containing file; only export higher when needed by multiple modules. |

---

## JS-NULL — Null Safety & Defaults

| Rule ID     | Severity | Rule                                                                                                            |
| ----------- | -------- | --------------------------------------------------------------------------------------------------------------- |
| JS-NULL-001 | HIGH     | Use nullish coalescing `??` instead of `\|\|` for defaults when falsy values (0, '', false) are valid.          |
| JS-NULL-002 | HIGH     | Use optional chaining `?.` for safe property access on nullable objects.                                        |
| JS-NULL-003 | MEDIUM   | Prefer template literals over string concatenation; ensure expressions don't evaluate to `undefined` or `null`. |

**How to scan:**

- Grep for `\|\| defaultValue` patterns where a falsy value like `0` or `''` would be a valid result.
- Grep for property access chains (`obj.a.b.c`) without optional chaining.
- Grep for string concatenation with `+` where template literals would be safer.

---

## JS-ERR — Error Handling

| Rule ID    | Severity | Rule                                                                                           |
| ---------- | -------- | ---------------------------------------------------------------------------------------------- |
| JS-ERR-001 | HIGH     | Use `try/catch` for every operation that may throw (JSON.parse, fs operations, network calls). |

**How to scan:**

- Grep for `JSON.parse(`, `JSON.stringify(`, `fs.readFile`, `fetch(` without a surrounding `try/catch`.

---

## JS-ASYNC — Async Patterns

| Rule ID      | Severity | Rule                                                                                          |
| ------------ | -------- | --------------------------------------------------------------------------------------------- |
| JS-ASYNC-001 | HIGH     | Use `Promise.all()` for parallel independent async operations — not sequential `await` calls. |
| JS-ASYNC-002 | HIGH     | Do not mix `async/await` with `.then()` in the same function.                                 |
| JS-ASYNC-003 | MEDIUM   | Return promises directly; don't `await` then immediately `return`.                            |
| JS-ASYNC-004 | MEDIUM   | Mark functions as `async` only if they contain an `await`.                                    |
| JS-ASYNC-005 | HIGH     | Use `AbortController` for cancellable async operations.                                       |

**How to scan:**

- Grep for functions containing both `await` and `.then(`.
- Grep for `return await` patterns (almost always unnecessary).
- Grep for sequential `await` calls that could be parallelised with `Promise.all`.
- Grep for `async` functions with no `await` keyword inside them.

---

## JS-PERF — Performance & Production Readiness

| Rule ID     | Severity | Rule                                                                               |
| ----------- | -------- | ---------------------------------------------------------------------------------- |
| JS-PERF-001 | LOW      | Prefer standard APIs over libraries when sufficient (native `fetch` over `axios`). |
| JS-PERF-002 | MEDIUM   | Lazy load non-critical resources — don't import everything at the top level.       |

### Memory Leaks — Closures & Callbacks

| Rule ID    | Severity | Rule                                                                                                                                                                           |
| ---------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| JS-MEM-001 | HIGH     | Prefer `.bind()` over arrow wrappers for long-lived listeners/timers. Arrow functions capture the full scope; `.bind(obj)` retains only `obj`.                                 |
| JS-MEM-002 | HIGH     | Use `{ once: true }` or pair every `addEventListener` with a `removeEventListener`. Unremoved listeners leak indefinitely.                                                     |
| JS-MEM-003 | HIGH     | Avoid closures that capture large objects (request bodies, buffers, ASTs) in timer callbacks, stream handlers, or subscription handlers. Extract only the minimal data needed. |
| JS-MEM-004 | HIGH     | Callbacks stored in long-lived collections (Maps, caches, registries) must not retain large objects. Verify the collection has eviction/cleanup logic.                         |

**How to scan:**

- Grep for `addEventListener` without a corresponding `removeEventListener` or `{ once: true }`.
- Grep for `setInterval` / `setTimeout` with arrow functions that reference large outer-scope objects.
- Grep for `.on('data',`, `.map(`, `.filter(` in stream pipelines with closures.

### General Runtime Performance

| Rule ID     | Severity | Rule                                                                                              |
| ----------- | -------- | ------------------------------------------------------------------------------------------------- |
| JS-PERF-003 | HIGH     | Avoid blocking the event loop with synchronous heavy computation — offload to workers or chunk.   |
| JS-PERF-004 | LOW      | Use `structuredClone()` over `JSON.parse(JSON.stringify(obj))` for deep cloning.                  |
| JS-PERF-005 | MEDIUM   | Do not allocate objects or arrays inside loops when they can be created once outside.             |
| JS-PERF-006 | MEDIUM   | Cache deeply nested property access before loops — don't re-traverse the chain on each iteration. |

**How to scan:**

- Grep for `JSON.parse(JSON.stringify(` patterns.
- Grep for object/array literal `{}` / `[]` initialisation inside `for`, `while`, `.forEach`, `.map` bodies.
- Grep for synchronous heavy operations (`readFileSync`, heavy computation loops) on the main thread.

---

## JS-VAL — Validation

| Rule ID    | Severity | Rule                                                                                               |
| ---------- | -------- | -------------------------------------------------------------------------------------------------- |
| JS-VAL-001 | HIGH     | Use a validation library (Zod, Joi, Yup) for complex runtime validation — not manual conditionals. |

**How to scan:**

- Grep for large chains of `if (x === undefined) ... if (typeof y !== 'string') ...` that could be replaced with a schema.
