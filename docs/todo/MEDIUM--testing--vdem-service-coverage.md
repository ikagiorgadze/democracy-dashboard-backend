---
id: tests-vdem-service
title: "Add tests for V-Dem query service"
severity: MEDIUM
type: testing
component: "src/services/vdem.service.ts"
status: open
estimate: "M"
dependencies: ["testing-foundation", "vdem-sql-injection"]
---

## Rationale
`queryVdemData` is a core data path that will soon be hardened against SQL injection. We need regression tests to lock in the fixes and verify behavior (year defaults, missing parquet handling). Without automated coverage, the service can regress to unsafe string interpolation or break expected defaults.

## Evidence
- src/services/vdem.service.ts:22-56 – builds SQL, applies default years, and throws when parquet is missing.
- No `*.test.ts` files or integration tests exercise this logic (`rg --files` confirms the absence of tests).

## Impact
Future refactors (e.g., parameterization, schema validation) may accidentally reintroduce insecure string concatenation or change default year ranges. Production incidents could go unnoticed until runtime.

## Proposed Change
Create unit/integration tests covering SQL construction via parameter binding, ensuring default year bounds, successful queries against a temp parquet, and failure when files are absent.

## Acceptance Criteria
- [ ] Tests assert sanitized SQL execution (e.g., malicious country strings do not break the query).
- [ ] Tests verify default start/end year behavior when inputs are omitted.
- [ ] Tests confirm the service throws an `AppError` when the parquet path is missing.
- [ ] Tests run in CI as part of `npm test`.

## Tasks
- [ ] Build a temporary parquet fixture (or mock DuckDB) for deterministic results.
- [ ] Add tests covering happy path, missing inputs, and injection attempts.
- [ ] Document the test cases alongside service docs.
- [ ] Ensure tests are idempotent and clean up temp files.

## Rollout & Risk
Add tests on a feature branch, run locally/CI, then merge with the security fix. No runtime risk once tests land.

## References
- src/services/vdem.service.ts
- docs/backend/services/vdem.md
