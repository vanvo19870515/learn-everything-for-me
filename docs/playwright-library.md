# Playwright Library vs Test Runner & Best Practices

## Library vs Test Runner

- **When to use**: `playwright` (Library) is a low-level API to launch browsers, handle contexts, pages, and manual assertions. `@playwright/test` is a full test runner with fixtures, builtin assertions, retries, reporters, and CLI tools.
- **Example difference**:
  - Library script needs to `chromium.launch()`, `context.newPage()`, `context.route`, `await page.title()` manually, and asserts via `node:assert`.
  - Test uses `test` fixture, `expect(page).toHaveTitle()`, auto-waits, and `npx playwright test` runs with configuration.
- **Extra features**: Playwright Test adds matrix projects, parallelism, web-first assertions, tracing, retries, reporting, and UI tooling.
- **Installation**:
  - Library: `npm install playwright` + `npx playwright install` (or install browser-specific packages).
  - Test Runner: `npm init playwright@latest` (includes configuration, test structure, CLI, and recommended workflow).

## Browser & System Setup

- **Install browsers**: `npx playwright install` for all, or specify `chromium`, `firefox`, `webkit`. Use `--with-deps` on CI. Use `PLAYWRIGHT_DOWNLOAD_HOST`, `HTTPS_PROXY`, or `NODE_EXTRA_CA_CERTS` when behind a proxy.
- **System dependencies**: `npx playwright install-deps` (+ browser name).
- **Hermetic mode**: `PLAYWRIGHT_BROWSERS_PATH=0 npx playwright install` or share browser cache via `PLAYWRIGHT_BROWSERS_PATH=$HOME/pw-browsers`.
- **Skip downloads**: `PLAYWRIGHT_SKIP_BROWSER_DOWNLOAD=1 npm install`.
- **Update Playwright**: `npm install -D @playwright/test@latest` + `npx playwright install`.

## Playwright Tooling

- **Codegen**: `npx playwright codegen <url>` records interactions and annotations, captures locators (`Pick Locator`), and supports devices, color schemes, viewport, timezone, geolocation, storage state (`--save-storage`, `--load-storage`). Useful for rapid test scaffolding or locator discovery.
- **UI Mode**: `npx playwright test --ui` launches interactive watch/debug experience with trace explorer.
- **Inspector**: `npx playwright test --debug` or `page.pause()` let you step through tests, edit locators, inspect DOM and logs.
- **Browser DevTools**: `PWDEBUG=console` exposes the Playwright helper in DevTools for debugging selectors.
- **VS Code Extension**: run/debug tests, pick locators, view logs/traces, and manage projects/profiles.

## Emulation & Actionability

- Leverage `devices` registry for viewport, UA, mobile, color scheme, geolocation, language, timezone, permissions, touch. Example:
  ```ts
  use: { ...devices['Desktop Chrome'], colorScheme: 'dark', geolocation: { latitude: 41.9, longitude: 12.5 } }
  ```
- Auto-wait ensures actions wait for visibility, stability, enabled/receiving events. `force: true` bypasses checks; use only when necessary.
- Use locators (`page.getByRole`, `getByLabel`, `getByText`, `getByTestId`) instead of brittle CSS/XPath. Chain `.filter()`, `.and()`, `.or()`, `.nth()`, `.first()`, `.last()` or layout selectors like `:right-of()`.

## Assertions & Expect API

- Prefer web-first assertions: `expect(locator).toBeVisible()`, `toHaveText()`, `toHaveURL()`, `toHaveScreenshot()`, `poll()`, `toPass()`.
- Non-retrying matchers: `expect(value).toBe()`, `.toEqual()`, `.toMatch()`.
- Soft assertions: `expect.soft(...)` to continue tests while collecting failures. Inspect `test.info().errors`.
- `expect.configure({ timeout, soft })` for specialized behavior; `expect.poll()` and `.toPass()` add custom retry logic.
- Extend expect with `expect.extend({ customMatcher() { ... } })`. Combine matchers from modules via `mergeExpects`.

## Fixtures & Configuration

- Built-in fixtures: `page`, `context`, `browser`, `request`, `testInfo`. Create custom fixtures via `test.extend`.
- Worker-scoped fixtures reduce duplication (`{ scope: 'worker' }`), and `auto: true` runs automatically for every test.
- Use `test.use({ storageState: 'state.json' })` to share logged-in state or reset state with `test.use({ storageState: { cookies: [], origins: [] } })`.
- Global setup: prefer project dependencies (setup project + dependencies + teardown). Alternatively configure `globalSetup`/`globalTeardown` functions returning teardown data.
- `test.describe.configure` controls mode (`serial`, `parallel`). Use `test.describe.serial()` for dependent tests (flaky).
- Filtering: `--grep`, `--grep-invert`, `--repeat-each`, `--shard`, `--only-changed`. Use `test.only`/`test.skip` or conditional `test.skip(condition)`.

## Network & API Testing

- **API requests**: Use `request` fixture with `baseURL`, `extraHTTPHeaders`, `proxy`. Setup/teardown with `beforeAll`/`afterAll` to create/delete resources.
- **Context vs global requests**: `context.request` shares cookies with browser context, `playwright.request.newContext()` is isolated. Use `request.storageState()` for reusable cookies.
- **Mocking**: `page.route()`, `context.route()` with glob/regexp, `route.fulfill/continue/abort`. Use HAR replay via `routeFromHAR()` or command-line `playwright open --save-har`.
- **WebSockets**: `page.routeWebSocket(url, handler)` to intercept and modify frames.
- **Proxy/auth**: configure `httpCredentials`, `proxy` fields globally or per-context.
- **Mock browser APIs**: `page.addInitScript()` to override `navigator.getBattery`, expose functions via `page.exposeFunction`, or simulate events manually.

## Accessibility & Snapshot Testing

- Use `expect(locator).toMatchAriaSnapshot()` to verify structured accessibility tree (`.aria.yml`). Update with `--update-snapshots`.
- Generate snapshots via Codegen or `locator.ariaSnapshot()`. Configure `expect.toMatchAriaSnapshot.pathTemplate`.
- Use `expect(locator).toHaveScreenshot()` for visual regression; configure `maxDiffPixels`, `stylePath`, `pathTemplate`.
- Store snapshots under `*.spec.ts-snapshots/`. Update with `npx playwright test -u`.
- Soft matching: `:text`, `:text-is`, `:text-matches`, partial matching via `[role=...]` and `/children: equal`.

## Traces, Videos, and Reports

- **Tracing**: configure `use.trace = 'on-first-retry'`, `'retain-on-failure'`, `'on'`, `'off'`. Use `npx playwright show-trace trace.zip` or `trace.playwright.dev` to inspect actions, DOM snapshots, timeline, console, network, attachments.
- **Videos**: `use.video = 'on-first-retry' | 'retain-on-failure' | 'on'`. Access via `page.video().path()` after context close.
- **Reporters**: combine `['html', { open: 'never' }]`, `blob`, `json`, `junit`, `dot`, `line`, `github`. Use `merge-reports` to stitch blob outputs, especially with sharding.
- **Artifacts**: upload HTML reports/traces/videos via `actions/upload-artifact`.

## Testing Strategies & Best Practices

- **Test pyramid & isolation**: unit/integration/E2E layering; keep tests isolated with independent BrowserContexts (or context per test). Use fixtures to share helper classes (e.g. POMs). Do not clean up by hand; create new contexts or delete storage.
- **Locators**: prefer `getByRole`, `getByText`, `getByLabel`; avoid brittle CSS/XPath. Use `getByTestId` for explicit test contracts. Use `locator.filter({ hasText })`, `.hasNot`, `.and`, `.or`.
- **Actions**: prefer `.fill()` over `.type()`/`.press()` unless necessary. Use `locator.hover()`, `dragTo()`, `mouse.wheel()` when needed. Use `locator.dispatchEvent()` for advanced keyboard/touch scenarios, multi-touch (pinch/pan), uploads via `setInputFiles`.
- **Auto-wait**: actions automatically wait for visibility/stability/enabled state; use `force: true` cautiously.
- **Authentication**: store storageState under `playwright/.auth`; authenticate via setup project or worker fixtures; use API login (request context) when possible; set `test.use({ storageState: auth.json })`.
- **Parallel workers**: use `testInfo.parallelIndex` to create unique accounts; manage per-worker storage states (`test.info().project.outputDir`).
- **Clock**: use `page.clock.setFixedTime()`, `.install()`, `.fastForward()`, `.pauseAt()`, `.runFor()` to control timers/Date/timeouts.
- **Components (experimental)**: use `@playwright/experimental-ct-*` to mount React/Vue/Svelte components (`mount`, `unmount`, `update`, `hooks`). Configure `ctViteConfig` if reusing existing Vite setup; use `hooksConfig` for routing, stores, Pinia.
- **Mock APIs**: use `router` fixture or MSW handlers, route WebSocket, use HAR for reproducible responses.
- **Service Workers**: disable with `serviceWorkers: 'block'` or interact via `browserContext.serviceWorkers()`; route Service Worker requests separately.
- **Autos**: prefer `page.getByRole('button', { name: 'Submit' })`. Use `expect.soft` for aggregated assertions, and `testInfo.setTimeout` for dynamic timeouts.

## Productivity Tips

- **Use `npx playwright test --workers=1`** or `--shard` for relative ordering/clusters.
- **Lint and type-check**: `npx tsc --noEmit`, `eslint` with `@typescript-eslint/no-floating-promises`.
- **CI**: run tests per-commit, parallelize via matrix/shards, upload blobs; configure `maxFailures`, `retries`, `workers`, `--forbid-only`.
- **Documentation**: keep README updated, store `.aria.yml` and screenshot snapshots in repo, commit trace/video attachments if needed.

## Additional Notes

- **Playwright Agents**: use `npx playwright init-agents --loop=vscode` to set up planner/generator/healer.
- **Canary builds**: install via `npm install -D @playwright/test@next` and view docs (press Shift x5 at playwright.dev) to test unreleased features.

