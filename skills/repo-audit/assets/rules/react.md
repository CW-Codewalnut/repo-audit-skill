# React Code Quality Skill

> **Scope:** All `.jsx` and `.tsx` files containing React components or hooks.  
> **Rule ID prefix:** `RX-`  
> Load after `assets/rules/universal.md`, `assets/rules/javascript.md`, and (for `.tsx`) `assets/rules/typescript.md`.

---

## RX-COMP — Component Structure

| Rule ID     | Severity | Rule                                                                                                        |
| ----------- | -------- | ----------------------------------------------------------------------------------------------------------- |
| RX-COMP-001 | HIGH     | Use functional components — avoid class components for new code unless required (Error Boundaries, legacy). |
| RX-COMP-002 | HIGH     | Keep components small and focused — split if large, complex, or doing multiple things.                      |
| RX-COMP-003 | HIGH     | Don't mix business logic, configuration, and UI in a single component — extract to helpers, utils, hooks.   |
| RX-COMP-004 | LOW      | Avoid unnecessary wrapper `<div>` elements — use `<>` fragments.                                            |

**How to scan:**

- Grep for `class ... extends Component` or `class ... extends PureComponent` in new code.
- Flag components longer than ~80 lines as candidates for splitting.

---

## RX-PROPS — Props

| Rule ID      | Severity | Rule                                                                                           |
| ------------ | -------- | ---------------------------------------------------------------------------------------------- |
| RX-PROPS-001 | HIGH     | Define prop types with TypeScript interfaces or types — be consistent throughout the codebase. |
| RX-PROPS-002 | MEDIUM   | Avoid passing too many props (more than 5–6 is a signal to use composition).                   |
| RX-PROPS-003 | HIGH     | Avoid prop drilling more than 2 levels deep — use context or composition.                      |

**How to scan:**

- Count props in component signatures — flag > 6 props.
- Trace props passed down through 3+ component levels (prop drilling).

---

## RX-STATE — State Management

| Rule ID      | Severity | Rule                                                                                        |
| ------------ | -------- | ------------------------------------------------------------------------------------------- |
| RX-STATE-001 | HIGH     | Keep state as local as possible — don't lift unnecessarily.                                 |
| RX-STATE-002 | HIGH     | Derive values from state rather than storing derived state (`useMemo`, computed variables). |
| RX-STATE-003 | NITPICK  | Use functional state updates `setX(prev => ...)` when new state depends on previous state.  |

**How to scan:**

- Look for `useState` holding values that are directly computable from other state/props.
- Look for `setX(!x)` — should be `setX(prev => !prev)` when `x` comes from state.

---

## RX-HOOKS — Hooks

| Rule ID      | Severity | Rule                                              |
| ------------ | -------- | ------------------------------------------------- |
| RX-HOOKS-001 | HIGH     | Keep hooks focused — one responsibility per hook. |
| RX-HOOKS-002 | MEDIUM   | Extract repeated hook logic into custom hooks.    |

### useEffect

| Rule ID    | Severity | Rule                                                                                                                                                                                            |
| ---------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RX-EFF-001 | HIGH     | One effect per concern — don't combine unrelated logic in a single `useEffect`.                                                                                                                 |
| RX-EFF-002 | CRITICAL | Don't lie about dependencies — include all reactive values in the dependency array.                                                                                                             |
| RX-EFF-003 | MEDIUM   | Move non-reactive code outside the effect body.                                                                                                                                                 |
| RX-EFF-004 | HIGH     | Question whether the `useEffect` is even needed — many can be replaced with event handlers or derived state.                                                                                    |
| RX-EFF-005 | CRITICAL | Every `useEffect` that acquires a resource (subscription, timer, observer, fetch) must return a cleanup function. An async effect without cleanup is a memory leak and state-after-unmount bug. |

**How to scan:**

- Grep for `useEffect(` and inspect every one:
  - Does it have a dependency array?
  - Does it return a cleanup function if it sets up anything async, a listener, timer, or subscription?
  - Are all reactive variables in the dependency array?

### useMemo & useCallback

| Rule ID     | Severity | Rule                                                                                        |
| ----------- | -------- | ------------------------------------------------------------------------------------------- |
| RX-MEMO-001 | MEDIUM   | Use `useMemo` only for genuinely expensive calculations — not as a default.                 |
| RX-MEMO-002 | MEDIUM   | Use `useCallback` for functions passed to optimised children or listed in `useEffect` deps. |

---

## RX-COND — Conditional Rendering

| Rule ID     | Severity | Rule                                                                                |
| ----------- | -------- | ----------------------------------------------------------------------------------- |
| RX-COND-001 | MEDIUM   | Extract complex conditions into named variables before JSX — not inline.            |
| RX-COND-002 | MEDIUM   | Use early returns for loading/error states at the top of the component.             |
| RX-COND-003 | MEDIUM   | Create separate components for complex conditional branches.                        |
| RX-COND-004 | HIGH     | Avoid deeply nested ternaries in JSX (more than 1 level of nesting is a violation). |

**How to scan:**

- Grep for `? ... : ... ? ... :` nested ternary chains inside JSX return statements.

---

## RX-LIST — Lists & Keys

| Rule ID     | Severity | Rule                                                                                |
| ----------- | -------- | ----------------------------------------------------------------------------------- |
| RX-LIST-001 | HIGH     | Never use array index as a key unless the list is truly static and never reordered. |
| RX-LIST-002 | HIGH     | Use unique, stable IDs from data as keys.                                           |
| RX-LIST-003 | LOW      | Extract list item rendering into a separate component when items are complex.       |

**How to scan:**

- Grep for `.map((item, index) =>` followed by `key={index}` — every hit is a violation unless the list is documented as static.

---

## RX-CTX — Context

| Rule ID    | Severity | Rule                                                                                   |
| ---------- | -------- | -------------------------------------------------------------------------------------- |
| RX-CTX-001 | HIGH     | Split contexts by concern — don't put everything in one giant context.                 |
| RX-CTX-002 | HIGH     | Memoize context values (`useMemo`) to prevent unnecessary re-renders of all consumers. |

**How to scan:**

- Grep for `createContext` and inspect what each context holds — flag contexts mixing unrelated data.
- Grep for `<SomeContext.Provider value={{ ... }}>` with an object literal that isn't memoized.

---

## RX-FORM — Forms

| Rule ID     | Severity | Rule                                                                                             |
| ----------- | -------- | ------------------------------------------------------------------------------------------------ |
| RX-FORM-001 | HIGH     | Single source of truth for form validation — schema-based (Zod + react-hook-form, Formik, etc.). |
| RX-FORM-002 | HIGH     | Prevent double submissions and race conditions — disable submit button while pending.            |
| RX-FORM-003 | LOW      | Use a form library for complex cross-field validation — don't hand-roll.                         |

---

## RX-MISC — Miscellaneous

| Rule ID     | Severity | Rule                                                                                            |
| ----------- | -------- | ----------------------------------------------------------------------------------------------- |
| RX-MISC-001 | LOW      | Use portals for modals, tooltips, and popovers — don't render in-tree where it breaks stacking. |
| RX-MISC-002 | MEDIUM   | Avoid inline styles unless dynamically required.                                                |
| RX-MISC-003 | HIGH     | Don't hardcode CSS colour values — use constants, themes, or CSS variables.                     |
| RX-MISC-004 | NITPICK  | In modern setups, the React namespace is globally available — don't import React unnecessarily. |

---

## RX-A11Y — Accessibility

| Rule ID     | Severity | Rule                                                                                                                                            |
| ----------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| RX-A11Y-001 | HIGH     | Never hardcode element `id` in reusable components — use `useId()` or accept an `id` prop.                                                      |
| RX-A11Y-002 | HIGH     | Use semantic HTML elements (`<button>`, `<nav>`, `<main>`) over generic `<div>` / `<span>`.                                                     |
| RX-A11Y-003 | HIGH     | Ensure full accessibility compliance (keyboard navigation, screen reader support).                                                              |
| RX-A11Y-004 | CRITICAL | Form controls must be programmatically associated with labels — `htmlFor`/`id` wiring must actually work; visual proximity alone is not enough. |
| RX-A11Y-005 | HIGH     | Changes to shared/reusable components must not degrade keyboard or assistive-tech access for any consumer.                                      |

**How to scan:**

- Grep for `<input`, `<select`, `<textarea` — verify each has either a wrapping `<label>` or a `htmlFor` + matching `id`.
- Grep for hardcoded `id="..."` in reusable components (appearing more than once, or in component files under `/components`).
- Grep for click handlers on `<div>` elements — should be `<button>` instead.

---

## RX-ERR — Error Boundaries

| Rule ID    | Severity | Rule                                                               |
| ---------- | -------- | ------------------------------------------------------------------ |
| RX-ERR-001 | HIGH     | Use Error Boundaries to catch rendering errors in component trees. |

**How to scan:**

- Check whether any `ErrorBoundary` component wraps key sections of the app.
- Flag apps with no `ErrorBoundary` at all as a `HIGH` finding.

---

## RX-PERF — Performance

| Rule ID     | Severity | Rule                                                                                                                                                       |
| ----------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RX-PERF-001 | HIGH     | Avoid inline object/function/array creation in JSX props — these create new references every render.                                                       |
| RX-PERF-002 | HIGH     | Move object/function/array creation outside the component body when closures aren't needed.                                                                |
| RX-PERF-003 | CRITICAL | Clean up every resource acquired in `useEffect`: object URLs, observers, timers, subscriptions, fetch calls. Use `AbortController` for in-flight requests. |

**How to scan:**

- Grep JSX attributes for `={{ ... }}` inline objects and `={() => ...}` inline functions — each is a candidate violation.
- Grep `useEffect` bodies for `fetch(`, `setInterval(`, `new IntersectionObserver(`, `addEventListener(` without a corresponding cleanup.

---

## RX-PROD — Production Readiness

| Rule ID     | Severity | Rule                                                                               |
| ----------- | -------- | ---------------------------------------------------------------------------------- |
| RX-PROD-001 | HIGH     | Use Tanstack Query, SWR, or similar for async data fetching — don't hand-roll.     |
| RX-PROD-002 | LOW      | Use battle-tested a11y libraries like Radix UI for complex interactive components. |

---

## RX-TEST — Component Testing

| Rule ID     | Severity | Rule                                                                                                         |
| ----------- | -------- | ------------------------------------------------------------------------------------------------------------ |
| RX-TEST-001 | HIGH     | Test component behaviour from the user perspective — not implementation details.                             |
| RX-TEST-002 | HIGH     | Use query priority: `role` > `label` > `placeholder` > `text` > `displayValue` > `alt` > `title` > `testId`. |
| RX-TEST-003 | HIGH     | Test user interactions with `userEvent` — not `fireEvent`.                                                   |
| RX-TEST-004 | HIGH     | Create a fresh `userEvent.setup()` per test or in `beforeEach` — not at suite scope.                         |
| RX-TEST-005 | HIGH     | Create fresh mock functions per test or reset in `beforeEach` — prevent call history leaking.                |
| RX-TEST-006 | HIGH     | Assert callback arguments, not just that the callback was called.                                            |
| RX-TEST-007 | HIGH     | Avoid index-based selectors (`getAllByRole(...)[0]`) — query by accessible name/label.                       |
| RX-TEST-008 | HIGH     | Use `waitFor` for async rendering assertions.                                                                |
