---
title: Playwright Fixtures & Config
summary: At-a-glance explanation of built-in fixtures, config types, and common helpers.
---

## Playwright Fixtures & Configuration

This page explains the core fixtures, configuration objects, and helpers that power `@playwright/test`. The goal is to make the lifecycle of a test easy to follow and give you quick access to the names you need when reading docs or writing Playwright tests.

## Fixtures

Playwright prepares fixtures for each test based on the parameters you request. The most common fixtures are:

| Fixture | What you get | Added |
| --- | --- | --- |
| `browser` | Shared browser per worker; use `browser.newPage()` when you need multiple pages. | v1.10 |
| `browserName` | Indicates the browser (`chromium`/`firefox`/`webkit`) currently running. Useful for conditional skips. | v1.10 |
| `context` | Isolated `BrowserContext` per test. `page` is created from this context automatically. | v1.10 |
| `page` | Fresh `Page` instance. Most tests use this fixture directly. | v1.10 |
| `request` | `APIRequestContext` for direct HTTP requests from the test. | v1.10 |

You can also configure built-in fixtures (`fixtures.browser`, `fixtures.context`, `fixtures.page`) and define your own with `test.extend()` or `test.use()`. `test.describe` groups inherit fixtures from their parents.

## Configuration Objects

Playwright exposes several runtime representations of your configuration:

- **`FullConfig`** – Resolved configuration available via `testInfo.config`. It mirrors the config file with additional metadata like `version`, `projects`, `workers`, `webServer`, etc.
- **`FullProject`** – Runtime view of a single project inside `FullConfig`. Use `testInfo.project` or `workerInfo.project` when you need project-level details such as `name`, `use`, `retries`, `outputDir`, or `teardown`.
- **`TestConfig`** – The shape of `playwright.config.ts`/`.js`. Use `defineConfig` to control options such as `timeout`, `retries`, `expect`, `projects`, and `use`.
- **`TestProject`** – Project-specific settings inside `testConfig.projects`. Each entry defines a subset of fixtures (`use`), tags, output directories, and execution directives.

Tip: `testInfo` (available in tests and hooks) provides `config`, `project`, `outputDir`, `snapshotDir`, and other helpers (like `attach()`, `skip()`, `setTimeout()`). `workerInfo` exposes the same data for worker-scoped fixtures.

## Test & Suite Helpers

`test` is the entry point for writing Playwright tests and provides advanced control beyond `it`:

| Feature | Purpose |
| --- | --- |
| `test()` | Declare async tests with fixtures. |
| `test.describe()` | Group tests, configure scoped fixtures/options (`test.describe.configure()`). |
| `test.use()` | Override fixtures or options inside a file/group (e.g., change `locale`, `colorScheme`). |
| `test.extend()` | Register custom fixtures/options and reuse them via a bespoke `test` export. |
| `test.beforeAll/afterAll/beforeEach/afterEach` | Lifecycle hooks with full fixture access (and `testInfo`). |
| `test.skip/fixme/slow/fail.only` | Declare test metadata or conditional behavior (tags, annotations, runtime decisions). |
| `test.step()` | Add named steps that appear in traces and reports; use `box` to surface failures at the step level. |
| `test.expect` | Access `expect` right from the `test` object for an alternate assertion import path. |

The helper methods accept optional `details` (tags, annotations, descriptions) and can be called with runtime decisions (`test.skip(condition, message)`).

## Test Options (`testOptions`)

`testConfig.use` & `testProject.use` control browser/context/page behavior. Highlights:

| Option | Purpose |
| --- | --- |
| `headless` | Run browsers in headless/headed mode. |
| `viewport` | Emulate screen size per test. |
| `baseURL` | Resolve navigation/assertion paths relative to a base. |
| `trace` | Record traces (`on-first-retry`, `retain-on-failure`, etc.). |
| `video` | Capture video (`on-first-retry`, `retain-on-failure`). |
| `screenshot` | Auto-capture screenshots (failure-only or every test). |
| `expect` | Configure assertion timeouts, screenshot thresholds, snapshot templates. |
| `contextOptions` | Fine-tune context creation (e.g., reduced motion, timezone). |
| `storageState` | Preload cookies/localStorage for authenticated flows. |

Many options accept fallback values (strings, booleans, objects) and can be overridden per-project or per-`test.use`.

## Hooks, TestInfo & Fixtures in Practice

- Use `test.beforeEach(async ({ page }, testInfo) => ...)` to prepare state. Inject `testInfo` for `outputPath`, `retry`, `annotations`, or skip/fail logic.
- Update the timeout inside a test via `testInfo.setTimeout(ms)` or `test.setTimeout(ms)` inside the body or hooks.
- Attach files/screenshots using `testInfo.attach()`/`testStepInfo.attach()` so reporters capture traces.
- Control retries with `test.describe.configure({ retries: 2 })` or `test.fail()` inside tests to document expected flakes.
- Use `test.step('name', async () => { ... })` to log readable steps in traces; mark them `box: true` when you want failures attributed to the step call.

## Quick links

- [Playwright Guide](Playwright_Guide_Reorganized.md)
- [Playwright Assertions](playwright-assertions.md)
