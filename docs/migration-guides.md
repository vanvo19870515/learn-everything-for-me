# Migration Guides & Infrastructure Notes

## Migrating from Protractor

### Principles
- No `webdriver-manager`/Selenium server: Playwright Test talks directly to browsers.
- `ElementFinder` & `waitForAngular` concepts are replaced by `page.locator()` + auto-waiting.
- Always `await` Playwright calls.

### Cheat sheet

| Protractor | Playwright Test |
| --- | --- |
| `element(by.css('...'))` | `page.locator('...')` |
| `element(by.buttonText('…'))` | `page.locator('button, input[type="button"], input[type="submit"] >> text="…"' )` |
| `element(by.repeater('todo in todoList.todos'))` | `page.locator('[ng-repeat="todo in todoList.todos"]')` |
| `element.all` | `page.locator(...)` (supports counts, nth, etc.) |
| `browser.get(url)` | `await page.goto(url)` |
| `browser.getCurrentUrl()` | `page.url()` |

### Example migration

Protractor test becomes Playwright Test with:
1. `test`/`expect` imports from `@playwright/test`.
2. Async `test` functions with injected `page` fixture.
3. `page.locator()` for DOM access and `expect(locator)` for assertions.
4. No manual Angular waits because Playwright auto-waits, with optional `waitForAngular` polyfill when needed (use `protractor`’s client-side script or Angular Testabilities).

## Migrating from Puppeteer

### Principles
- Playwright is multi-browser; swap `puppeteer.launch()` for specific `playwright.chromium/firefox/webkit.launch()`.
- Prefer Playwright `Locator` + web-first assertions over `page.$`, `page.$eval`, and `ElementHandle`.
- Auto-wait removes most manual `waitFor*` calls, though `waitForLoadState()` or assertions with `await` are available.

### Cheat sheet

| Puppeteer | Playwright Library |
| --- | --- |
| `puppeteer.launch()` | `playwright.chromium.launch()` |
| `page.waitForSelector()` | `locator.waitFor()` (or auto via `await locator.click()`) |
| `page.$eval()` | `expect(locator).toHaveText()` |
| `page.click(selector)` | `page.locator(selector).click()` |
| `page.type(...)` | `.fill(...)` |
| `page.waitForNavigation()` | usually unnecessary; use `await page.goto()` or `expect(page).toHaveURL()` |
| `page.setViewport(...)` | `page.setViewportSize(...)` |
| `page.reload()` | `await page.reload()` (auto-waiting) |

### Example migration
- Replace Puppeteer/Jest script with Playwright Test: import `{ test, expect }`, use `test()` blocks receiving `{ page }`, and assert with `expect`.
- Drop manual `waitForSelector` in favor of `await expect(locator).toBeVisible()` or `toHaveCount`.
- `setViewportSize`, `newContext`, `newPage`, and `auto-waited` locators replace the older flows.

## Migrating from Testing Library (Component Testing)

### Principles
- Replace `render` + queries with `mount` and Playwright locators from `@playwright/experimental-ct-*`.
- Use Playwright actions/locators (`getByText`, `getByRole`, `fill`, `click`) instead of `screen` helpers.
- Assertions rely on `expect(locator)` (web-first) rather than DOM texts/`queryBy`.

### Cheat sheet

| Testing Library | Playwright Component Testing |
| --- | --- |
| `render(<Component />)` | `await mount(<Component />)` |
| `screen.getByText(...)` | `component.getByText(...)` |
| `user.click(...)` | `locator.click()` |
| `screen.findByText` | `component.getByText(...)` (auto-waiting) |
| `screen.getByLabelText` | `component.getByLabel('...')` |
| `expect(...).toBeInTheDocument()` | `await expect(locator).toBeVisible()` |
| `within(...)` | `locator.locator(...)` |

### Tips
- Use `update()` to rerender with new props, `unmount()` to clean up, `hooksConfig` for dependencies (routing, stores).
- Component tests reuse fixtures and run in isolation via `mount`.

## Docker & CI Notes

### Docker
- Prefer `mcr.microsoft.com/playwright:<version>-noble` containers; include `seccomp_profile.json` when running as non-root.
- Use recommended flags: `--init`, `--ipc=host`, `--cap-add=SYS_ADMIN` (during debugging), run headed tests with `xvfb-run`.
- Playwright Server: `npx playwright run-server --port ...` in container, connect from host via `PW_TEST_CONNECT_WS_ENDPOINT`.
- For scraping/untrusted code use separate user (`--user pwuser`) plus seccomp profile for Chromium sandbox support.

### CI Best Practices
- Install dependencies: `npm ci` + `npx playwright install --with-deps`.
- Prefer `workers: process.env.CI ? 1 : undefined`, but parallelize on powerful runners; use `--shard=x/y` or CI matrix for splitting.
- Use `--only-changed` for fail-fast previews, run full suite afterward.
- Upload `playwright-report`, traces, videos via `actions/upload-artifact`.
- Configure reporters like `[['junit', { outputFile: 'test-results/results.xml' }], ['html', { open: 'never' }]]`.
- Increase `globalTimeout`, `maxFailures`, or `retry` as needed for stability.
- Set `DEBUG=pw:browser*` for detailed logs when diagnosing grid/launch issues.

### CI Providers
- **GitHub Actions**: workflows described; use container image, deployment triggers, matrix/sharding, and publish artifacts.
- **Azure Pipelines**: install Node.js (UseNode task), run `npx playwright test`, upload reports, optionally publish JUnit/artifacts.
- **CircleCI/GitLab/Bitbucket/Drone/Google Cloud Build**: use `mcr.microsoft.com/playwright` image and run `npx playwright test`; use `parallel`/`matrix` for sharding.
- **Docker/CI**: prefer caching? not necessary; let Playwright download browsers unless pinning is critical.
- **Fail-fast**: run `npx playwright test --only-changed=$BASE_REF` on PRs before full suite.

## Selenium Grid (Experimental)

- Playwright can connect to Selenium Grid 4 via `SELENIUM_REMOTE_URL` (Chromium/Edge only) and optional `SELENIUM_REMOTE_CAPABILITIES`/`SELENIUM_REMOTE_HEADERS`.
- Provide grid-specific env vars (`SE_NODE_GRID_URL`, `SE_EVENT_BUS_*`) when starting nodes.
- Use `DEBUG=pw:browser*` for low-level logs and include them when filing issues.
- Docker example: run standalone/node containers, then `SELENIUM_REMOTE_URL=http://hub:4444 npx playwright test`.
- Selenium 3 support is best-effort; direct grid access required because CDP is not exposed.

## Supported Languages & Tools

| Language | Recommended Runner | Notes |
| --- | --- | --- |
| JavaScript/TypeScript | `@playwright/test` | Built-in test runner with tracing, reporters, inspector, CLI. |
| Python | `playwright pytest plugin` | Use `pytest` fixture helpers for browsers + parallelism. |
| Java | Any test framework (JUnit/TestNG) | Use Playwright Java bindings with webdriver style. |
| .NET | MSTest/NUnit/xUnit | Playwright for .NET offers base classes per runner. |

Include matching Playwright version across languages/containers for compatibility. If using Docker, ensure `npx playwright test` version matches browser binaries inside image.

