---
title: Playwright Reporter API
summary: Custom reporter lifecycle, hooks, and schema reference for Playwright Test.
---

## Playwright Reporter API

Playwright notifies reporters about test execution events, giving you a chance to log, collect artifacts, or publish custom dashboards. This guide covers the lifecycle, the key hooks you can implement, and how the main data structures (`FullConfig`, `Suite`, `TestCase`, `TestResult`, `TestStep`) flow through a run.

### Create a reporter

Implement a class that satisfies `Reporter`. Export it as the default and configure your `playwright.config.ts`:

```ts
import type { Reporter, FullConfig, Suite, TestCase, TestResult, FullResult } from '@playwright/test/reporter';

export default class MyReporter implements Reporter {
  constructor(options: { customOption?: string } = {}) {
    console.log('Reporter ready', options.customOption);
  }

  onBegin(config: FullConfig, suite: Suite) {
    console.log(`Running ${suite.allTests().length} tests`);
  }

  onTestBegin(test: TestCase) {
    console.log('Starting', test.title);
  }

  onTestEnd(test: TestCase, result: TestResult) {
    console.log('Finished', test.title, result.status);
  }

  onEnd(result: FullResult) {
    console.log('Run finished with', result.status);
  }
}
```

```ts
// playwright.config.ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  reporter: [['./my-awesome-reporter.ts', { customOption: 'value' }]],
});
```

Set `printsToStdio()` to `false` if your reporter emits no terminal output; Playwright will add a default reporter for CLI feedback.

### Lifecycle hooks

| Hook | When it fires | Purpose |
| --- | --- | --- |
| `onBegin(config, suite)` | After discovery, before running tests | Inspect `suite` hierarchy and config metadata. |
| `onTestBegin(testCase)` | When each test run starts | Log/test instrumentation. |
| `onStepBegin(testCase, result, step)` | Each `test.step()` begins | Highlight nested step info. |
| `onStdOut(chunk, testCase?, result?)` | Worker writes to stdout | Capture console output per test. |
| `onStdErr(...)` | Worker writes to stderr | Same for stderr. |
| `onTestEnd(testCase, result)` | After a test finishes | Access final status, errors, attachments. |
| `onEnd(fullResult)` | Once suites finished | Optionally override `result.status`/exit code. |
| `onExit()` | Just before process exit | Upload collected reports, clean up. |
| `onError(error)` | When global error occurs | Surface issues outside tests. |

`result` is gradually populated (stdout, steps, attachments), so read it after `onTestEnd`.

### Reporting data model

- `Suite` – hierarchical container (root → project → file → describe). Use `suite.allTests()` to iterate every `TestCase`.
- `TestCase` – representation of a `.test()` call per project/shard. Inspect `testCase.title`, `location`, `retry`, `tags`, and `results`.
- `TestResult` – concrete run of a test. Contains `status`, `error`, `steps`, `stdout`, `stderr`, `attachments`, and timing (`duration`, `startTime`, `workerIndex`).
- `TestStep` – steps inside assertions/hooks. Each step has `titlePath`, `category`, `duration`, `error`, and optional attachments.
- `FullConfig` / `FullProject` – resolved config details available in `onBegin` and via `testInfo`.

### Additional tips

- Wrap reporter logic in `try/catch`; Playwright swallows exceptions, so you must rethrow/wrap if you want the run to fail on reporter issues.
- The `merge-reports` CLI replays the Reporter API, but each shard becomes its own `FullProject`. Treat them as separate entries when aggregating.
- Use `printsToStdio()` to advertise whether your reporter writes to the terminal. Return `false` for purely machine-readable outputs so Playwright adds a default CLI reporter.

### Quick links

- [Playwright Guide](Playwright_Guide_Reorganized.md)
- [Playwright Fixtures & Config](playwright-fixtures.md)
- [Playwright Assertions](playwright-assertions.md)

