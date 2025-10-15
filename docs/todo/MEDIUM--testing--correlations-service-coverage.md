---
id: tests-correlations-service
title: "Add tests for correlation parquet access"
severity: MEDIUM
type: testing
component: "src/services/correlations.service.ts"
status: open
estimate: "M"
dependencies: ["testing-foundation", "correlations-path-traversal"]
---

## Rationale
`getCorrelation` and `getTopCorrelations` perform path derivation, DuckDB reads, and ranking logic that are critical to the analysis endpoints. With upcoming security fixes on path traversal, we need automated tests that validate containment checks, dataset filtering, and ranking rules.

## Evidence
- src/services/correlations.service.ts:19-176 – path resolution, SQL construction, ranking logic.
- src/services/correlations.service.ts:179-321 – dataset filtering and ordering.
- No tests currently exercise these code paths.

## Impact
Without tests, path sanitization could regress, letting directory traversal slip back in. Ranking or filter changes might quietly break the analysis UI. Missing coverage also risks performance regressions (e.g., failing to detect missing part files).

## Proposed Change
Add tests that simulate the correlations directory with temp parquet files (or mocked DuckDB) to verify safe path handling, dataset prefix filtering, min observation enforcement, and ranking behavior.

## Acceptance Criteria
- [ ] Tests assert that malicious country inputs are rejected post-sanitization.
- [ ] Tests cover scenarios with multiple parquet entries to confirm ranking and dataset filtering logic.
- [ ] Tests verify proper handling when country directories or part files are missing (returns null/[] without throwing).
- [ ] Tests run as part of `npm test`.

## Tasks
- [ ] Create fixtures or mocks for correlation parquet data.
- [ ] Implement tests for both `getCorrelation` and `getTopCorrelations`.
- [ ] Ensure tests clean up temporary directories.
- [ ] Document test coverage expectations alongside service docs.

## Rollout & Risk
Add tests incrementally while implementing the traversal fix to maintain momentum. No runtime risk.

## References
- src/services/correlations.service.ts
- docs/backend/services/correlations.md
