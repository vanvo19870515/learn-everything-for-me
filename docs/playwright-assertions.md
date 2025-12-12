---
title: Playwright Assertion APIs
summary: Quick reference for the built-in assertion helpers exposed by `expect()`.
---

## Playwright Assertion APIs

This page distills the most important assertion helpers that Power Playwright tests. It groups them by the helper class (`APIResponseAssertions`, `GenericAssertions`, `LocatorAssertions`, `PageAssertions`, and `SnapshotAssertions`) and highlights their intent, common options, and usage patterns so you can pick the right helper quickly.

## `APIResponseAssertions`

`expect(response)` returns an `APIResponseAssertions` instance that focuses purely on HTTP response metadata.

| Method | Added in | Intent | Example |
| --- | --- | --- | --- |
| `toBeOK()` | v1.18 | Ensures the status code is in the `200..299` range using Playwright's automatic retry logic. | `await expect(response).toBeOK();` |

The `.not` modifier flips the assertion (e.g., `await expect(response).not.toBeOK()`).

## `GenericAssertions`

`expect(value)` without any additional import yields `GenericAssertions`, the most versatile bucket. It supports deep equality, pattern matching, and numeric comparisons.

### Deep/composite assertions

| Method | Added in | Purpose |
| --- | --- | --- |
| `toBe()` | v1.9 | Reference equality (`===`). |
| `toEqual()` | v1.9 | Deep equality with optional matchers (`expect.any`, `.arrayContaining`, etc.). |
| `toStrictEqual()` | v1.9 | Deep equality including `undefined` keys, sparseness, and constructor identity. |
| `toMatchObject()` | v1.9 | Partial object match that allows extra properties. |
| Pattern matchers (`expect.objectContaining`, `.arrayContaining`, `.arrayOf`, `.stringContaining`, `.stringMatching`, `.anything()`, `.any()`, `.closeTo()`) | v1.9+ | Relaxed matching helpers to mix into `toEqual`. |

### Numeric, truthy/falsy, and type guards

| Method | Added in | Intent |
| --- | --- | --- |
| `toBeCloseTo()` | v1.9 | Float comparison with optional precision. |
| `toBeGreaterThan()` / `toBeGreaterThanOrEqual()` | v1.9 | Numeric comparisons (`>` / `>=`). |
| `toBeLessThan()` / `toBeLessThanOrEqual()` | v1.9 | Numeric comparisons (`<` / `<=`). |
| `toBeNaN()` | v1.9 | Ensures the value is `NaN`. |
| `toBeDefined()` / `toBeUndefined()` / `toBeNull()` | v1.9 | Basic presence checks. |
| `toBeTruthy()` / `toBeFalsy()` | v1.9 | Boolean-context checks that ignore the specific value. |
| `toContain()` / `toContainEqual()` | v1.9 | Collection membership, either by reference or deep equality. |
| `toHaveLength()` | v1.9 | Ensures `value.length === expected`. |
| `toHaveProperty()` | v1.9 | Ensures nested property exists (optionally matching value). |
| `toMatch()` | v1.9 | RegExp match on strings. |
| `toThrow()` / `toThrowError()` | v1.9 | Asserts a function throws (optionally matching error message/instance). |

All `GenericAssertions` expose `.not` to invert the check (e.g., `expect(value).not.toBe(42)`).

## `LocatorAssertions`

`expect(locator)` returns `LocatorAssertions`, which auto-retries until the page stabilizes. These helpers are the cornerstone of web-first validation.

| Method | Added in | Description |
| --- | --- | --- |
| `toBeVisible()` / `toBeHidden()` | v1.20 | Visibility state, waits for actability, optional timeout. |
| `toBeEnabled()` / `toBeDisabled()` | v1.20 | Control enablement. |
| `toBeChecked()` | v1.20 | Checkbox/radio checked state (supports `indeterminate`). |
| `toBeEditable()` / `toBeEmpty()` | v1.20 | Input writability and emptiness. |
| `toBeAttached()` | v1.33 | Element connected to DOM (document or shadow root). |
| `toBeFocused()` | v1.20 | Element has document focus. |
| `toBeInViewport()` | v1.31 | Ensures the element intersects the viewport (with optional ratio). |
| `toHaveText()` | v1.20 | Assert (optionally case-insensitive) text content or arrays of texts. |
| `toContainText()` | v1.20 | Partial text match within a set of nodes. |
| `toHaveClass()` / `toContainClass()` | v1.20 / v1.52 | Class-list assertions. |
| `toHaveAttribute()` / `toHaveAttribute(name)` | v1.20 / v1.39 | Attribute presence/value checks. |
| `toHaveId()` | v1.20 | ID check (supports RegExp). |
| `toHaveRole()` | v1.44 | ARIA role matching. |
| `toHaveCSS()` | v1.20 | Computed style value assertion. |
| `toHaveCount()` | v1.20 | Number of nodes resolved by locator. |
| `toHaveValues()` | v1.23 | Multi-select values. |
| `toHaveValue()` | v1.20 | Input value (supports RegExp). |
| `toHaveJSProperty()` | v1.20 | JavaScript property on DOM node. |
| `toHaveScreenshot()` | v1.23 | Visual regression that waits for stable screenshots, optionally masks areas, hides caret, and compares pixel diff thresholds. |
| `toMatchAriaSnapshot()` | v1.49 | Accessibility snapshot assertions stored as `.aria.yml` files; accepts inline string or references fixture. |

Each method accepts optional `timeout` (ms) that defaults to the expectation timeout defined in your config. Many also accept method-specific flags such as `ignoreCase`, `mask`, or `ratio`. Use `locator.first()`/`.nth()` to narrow collections before asserting.

## `PageAssertions`

`expect(page)` returns helpers scoped to the entire `Page`.

| Method | Added in | Description |
| --- | --- | --- |
| `toHaveURL()` | v1.20 | Match URL string, regex, or predicate with optional `ignoreCase`. |
| `toHaveTitle()` | v1.20 | Document title match (string or regex). |
| `toHaveScreenshot()` | v1.23 | Page-level visual regression (same options as locator version). |

`.not` works on every helper (e.g., `await expect(page).not.toHaveURL(/error/)`).

## `SnapshotAssertions`

Snapshots operate on raw buffers/strings (typically screenshots or serialized values). `expect(value).toMatchSnapshot(name)` compares the value against the file stored in your test snapshots folder. The `options` parameter can tweak thresholds, provide explicit names, or generate structured paths.

| Method | Added in | Notes |
| --- | --- | --- |
| `toMatchSnapshot(name)` | v1.22 | Provide name or array path; best for serialized objects and non-visual snapshots (visual comparisons should use `.toHaveScreenshot`). |
| `toMatchSnapshot(options)` | v1.22 | Let Playwright derive the name from the test, with optional `threshold`, `maxDiffPixels`, and `maxDiffPixelRatio`. |

Snapshot assertions only run inside the Playwright test runner; they are helpful for catching regressions that are hard to describe with single-line sanity checks.

## Jump to

- [Playwright Guide](Playwright_Guide_Reorganized.md)
- [Playwright Agents & Canary Workflows](playwright-agents.md)
- [Playwright Library vs Test Runner](playwright-library.md)
