---
id: correlations-path-traversal
title: "Sanitize correlation country paths"
severity: BLOCKER
type: security
component: "src/services/correlations.service.ts"
status: open
estimate: "M"
dependencies: []
---

## Rationale
Both correlation endpoints call `getCorrelation` / `getTopCorrelations`, which derive parquet directories by blindly joining `CORRELATIONS_DIR` with the user-supplied `country` string. The docs describe these endpoints as public API surface for the dashboard, so the inputs are attacker-controlled. Because `path.join` accepts segments with `../`, a crafted country like `"../../../../etc"` escapes the correlations directory. This violates the security expectations around file-backed datasets.

## Evidence
- src/services/correlations.service.ts:40-54,195-208
  ```ts
  const countryTrim = country.trim();
  const candNames = [
    `country_name=${countryTrim}`,
    `country_name-${countryTrim}`,
  ];
  for (const n of candNames) {
    const p = path.join(CORRELATIONS_DIR, n);
    const stat = await fs.stat(p);
    …
  }
  ```
- No sanitization or containment check is performed before hitting the filesystem.

## Impact
An attacker can probe for the existence of directories outside the parquet tree, potentially exfiltrate parquet content from other tenants, or trigger unexpected behavior by pointing DuckDB at attacker-chosen glob patterns. At best this leaks filesystem structure; at worst it escalates to arbitrary file reads when DuckDB follows the supplied paths.

## Proposed Change
Normalize and validate the `country` input before touching the disk. Reject any value containing path separators or traversal sequences. Resolve candidate paths and ensure they remain within `CORRELATIONS_DIR` (e.g., via `path.resolve` plus prefix check). Consider mapping countries to known slugs to remove raw user strings from the filesystem layer altogether.

## Acceptance Criteria
- [ ] Correlation services reject country values with `..`, `/`, or `\`.
- [ ] Resolved parquet paths are verified to be inside `CORRELATIONS_DIR`.
- [ ] Unit tests cover traversal attempts and confirm 400/404 responses.
- [ ] Documentation clarifies expected country formats or slug mapping.

## Tasks
- [ ] Introduce a sanitizer/lookup for country identifiers used in correlation lookups.
- [ ] Add defensive checks before `fs.stat` / `fs.readdir`.
- [ ] Update controllers to translate invalid inputs into client errors.
- [ ] Document the sanitization in docs/backend/services.md or similar.

## Rollout & Risk
This is an additive hardening change. Deploy normally; monitor for increased 400 responses that may indicate clients sending unexpected country strings. If legitimate traffic fails, adjust the sanitizer's allowlist while keeping traversal protection intact.

## References
- docs/backend/services/correlations.md
- src/controllers/analysis.controller.ts
- src/services/correlations.service.ts
