Title: feat(a11y): add end-to-end keyboard & screen-reader CI tests (#1260)

Summary
- Adds end-to-end accessibility tests that exercise keyboard navigation and basic screen-reader-friendly checks across representative pages.
- Runs tests locally via Playwright and in CI via GitHub Actions on pull requests and pushes to `feature/robust-csp`.
- Tests are intentionally conservative for now (enforce existence of a main landmark and record image/control issues) to be stable against the site's current baseline while providing actionable telemetry.

What I changed
- tests/visual/accessibility/keyboard-screenreader.spec.ts
  - New Playwright tests that:
    - Tab through pages to validate keyboard focus behavior.
    - Run DOM-based accessibility checks (main landmark present, report counts for images missing alt text and interactive controls missing accessible names).
- package.json
  - Added script: `test:e2e:accessibility` to run the new tests.
  - Added `axe-core` dev dependency (used for future upgrades; tests currently use DOM checks to avoid CSP/script-injection issues).
- .github/workflows/e2e-accessibility.yml
  - CI workflow to run the accessibility E2E tests on PRs against `main` and pushes to `feature/robust-csp`.

Why
- Provides an automated CI guard for keyboard navigation and basic screen-reader checks.
- Helps catch regressions early and provides maintainers with quantitative telemetry (counts of missing alts and unnamed controls).
- Designed to be non-flaky and not block the team while we iteratively harden accessibility across the site.

How I verified
- Installed Playwright and run the tests locally:
  - npm install
  - npx playwright install --with-deps
  - npm run test:e2e:accessibility
- Confirmed all tests pass locally (14 passed).
- Committed the tests and workflow and pushed to `feature/robust-csp`.

Notes & next steps
- Current tests enforce a main landmark (failure) and log counts for other accessibility issues so maintainers can triage and fix incrementally. If you want stricter enforcement (e.g., fail on missing alt attributes or unnamed controls), I can tighten the assertions after fixing the initial violations.
- Recommended follow-ups:
  - Add a weekly/a PR-level report that summarises a11y issues across the site.
  - Incrementally fix high-impact violations surfaced by these checks.
  - Optionally re-enable axe-core full scans (currently avoided because of CSP/injection constraints); we can do that after relaxing CSP for test environments or loading axe via trusted CDN in CI.

Files changed (high level)
- Added: `tests/visual/accessibility/keyboard-screenreader.spec.ts`
- Added: `.github/workflows/e2e-accessibility.yml`
- Updated: `package.json`

Local commands to run (copy/paste)
```bash
npm install
npx playwright install --with-deps
npm run test:e2e:accessibility
```

Please review and merge.
