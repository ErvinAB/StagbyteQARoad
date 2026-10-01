# Environment Setup

Issue: ErvinAB/StagbyteQARoad#11 · Machine: MacBook (Apple Silicon M4), macOS 27.0.1

## Installed tools

| Tool | Version | Check command | Status |
|---|---|---|---|
| Git | 2.54.0 | `git --version` | ✅ |
| Node.js | v26.0.0 | `node --version` | ✅ (26 enters LTS in October 2026) |
| npm | 11.12.1 | `npm --version` | ✅ |
| Homebrew | 7.0.6 | `brew --version` | ✅ |
| VS Code | installed | — | ✅ (`code` shell command not set up yet) |
| Playwright | 1.63.0 | `npx playwright --version` | ✅ Chromium and WebKit, ❌ Firefox (see Known issues) |
| Docker | — | `docker --version` | ⏸ Deferred to Phase 5 (#33) |
| Postman / Bruno | — | — | ⏸ Deferred to Phase 2 (#19) |

## Setup on a clean Mac

1. Install Homebrew: https://brew.sh
2. Install Git and Node.js:
   ```bash
   brew install git node
   ```
3. Clone the repository and install dependencies:
   ```bash
   git clone https://github.com/ErvinAB/StagbyteQARoad.git
   cd StagbyteQARoad
   npm ci
   ```
4. Download Playwright's own browsers (separate from any browsers already installed):
   ```bash
   npx playwright install
   ```
5. Run the example tests:
   ```bash
   npx playwright test
   ```
   Expected: `4 passed`.
6. Open the HTML report (Ctrl+C to stop it):
   ```bash
   npx playwright show-report
   ```

`npm ci` installs the exact versions recorded in `package-lock.json`, so every machine gets the same setup. `node_modules/` and test output folders are excluded via `.gitignore`.

## Evidence

```
$ npx playwright test

Running 4 tests using 4 workers
  4 passed (1.5s)
```

Tests passed: `has title` and `get started link`, on Chromium and WebKit.

## Known issues

### Firefox fails to launch on macOS 27

- **Symptom:** every Firefox test fails with `browserType.launch: Failed to launch the browser process` / `Could not find profile folder.`
- **Cause:** Playwright's bundled Firefox reads the real Firefox data folder (`~/Library/Application Support/Firefox`), which macOS 27 protects from programs started in a terminal. It only happens when Firefox (any edition) is installed on the machine.
- **Upstream bug:** https://github.com/microsoft/playwright/issues/42768 (fixed in Firefox 158; not yet in a Playwright release)
- **Reproduced:** Playwright 1.63.0, `firefox-1543`, macOS 27.0.1. It also fails when Firefox is launched directly, without Playwright.
- **Workaround:** Firefox project commented out in `playwright.config.ts`. Granting the terminal Full Disk Access also works, but it was rejected as too broad a permission.
- **Next step:** re-enable Firefox after upgrading to a Playwright release that ships Firefox 158 or newer.

## Security

No credentials, tokens or secrets are stored in this repository.
