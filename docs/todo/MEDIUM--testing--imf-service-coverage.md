---
id: tests-imf-service
title: "Add tests for IMF service flows"
severity: MEDIUM
type: testing
component: "src/services/imf.service.ts"
status: open
estimate: "L"
dependencies: ["testing-foundation", "imf-fields-null-guard"]
---

## Rationale
`queryImfData` performs complex work—country code normalization, decomposing XML responses, applying timeouts, and aggregating series. Today none of these behaviors are tested. Given the manual validation and forthcoming fixes, automated tests are needed to prevent regressions and to ensure country resolution logic handles edge cases.

## Evidence
- src/services/imf.service.ts:40-142 – country map loading/caching, string normalization, fetch logic.
- No existing test files, and package.json lacks `test` scripts.

## Impact
Changes to country mapping data or the resolver can silently break API responses (wrong ISO codes, case sensitivity bugs). Timeout adjustments or XML parser tweaks may introduce subtle errors. Without tests, these issues surface only in production.

## Proposed Change
Create isolated unit tests for `resolveCountry`, `loadCountryMap`, and XML parsing, plus integration-style tests that mock `fetch` to verify aggregation behavior, timeout handling, and error propagation.

## Acceptance Criteria
- [ ] Unit tests cover country normalization (ISO codes, fuzzy matches, ambiguous names, failure cases).
- [ ] Tests simulate IMF XML responses (including missing values) and confirm parsed output.
- [ ] Tests assert timeout behavior and AppError propagation for failed HTTP calls.
- [ ] Tests run under `npm test` without network access (use fetch mocks/fixtures).

## Tasks
- [ ] Add fixtures for representative IMF XML responses.
- [ ] Mock `fetch` and `AbortController` to exercise success and failure paths.
- [ ] Cover caching behavior (country map reload, reverse map).
- [ ] Document scenarios in docs/backend/services/imf.md testing section.

## Rollout & Risk
Purely additive. Ensure tests are stable across platforms and do not hit real IMF endpoints.

## References
- src/services/imf.service.ts
- docs/backend/services/imf.md
