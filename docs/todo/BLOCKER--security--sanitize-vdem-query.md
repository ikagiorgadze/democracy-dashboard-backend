---
id: vdem-sql-injection
title: "Parameterize V-Dem parquet queries"
severity: BLOCKER
type: security
component: "src/services/vdem.service.ts"
status: open
estimate: "M"
dependencies: []
---

## Rationale
`queryVdemData` constructs DuckDB SQL by concatenating user-provided `countries` and `fields`. Documentation expects controllers to enforce validation, but today they only perform shallow checks. Crafted inputs can therefore break the query or execute arbitrary SQL against the DuckDB engine that has access to the host filesystem. This violates the security expectations for dataset queries and contradicts the docs' intention for controllers to enforce validation.

## Evidence
- src/services/vdem.service.ts:38-47
  ```ts
  const fieldList = ["country_name", "year", ...fields]
    .map((field) => `"${field}"`)
    .join(", ");
  const countryList = countries.map((c) => `'${c}'`).join(", ");
  const sql = `
    SELECT ${fieldList}
    FROM read_parquet('${parquetPath}')
    WHERE country_name IN (${countryList})
      AND year BETWEEN ${start} AND ${end}
  `;
  ```
- User inputs with quotes (e.g., `countries: ["US'); COPY (SELECT 1) TO '/tmp/x' --"]`) break out of the string literal today.

## Impact
Attackers can read arbitrary parquet files the service account can access or execute other DuckDB commands. At minimum this is a denial of service vector; at worst it becomes arbitrary file exfiltration. It also prevents legitimate queries for countries with apostrophes (e.g., "Côte d'Ivoire"), undermining data access.

## Proposed Change
Replace manual interpolation with parameterized DuckDB statements or use the DuckDB `prepare` API. Validate `fields` against the parquet schema and reject unexpected column names. Normalize country names and pass them as bound parameters. Add controller-level schema validation (e.g., zod) to ensure types and lengths.

## Acceptance Criteria
- [ ] `queryVdemData` builds SQL without directly interpolating raw user strings.
- [ ] Requests containing quotes in countries/fields no longer break the query.
- [ ] Automated tests cover typical payloads plus malicious strings.
- [ ] Controller rejects unknown columns or malformed payloads with 400s.

## Tasks
- [ ] Introduce schema validation for V-Dem requests (libraries or custom).
- [ ] Use bound parameters or safe templating for DuckDB query construction.
- [ ] Add negative tests proving injection attempts are neutralized.
- [ ] Document the validation rules in docs/backend/controllers or similar.

## Rollout & Risk
Deploy behind feature tests; run regression tests for nominal queries. Monitor logs for new 400 errors signalling stricter validation. If regressions appear, toggle back to the previous implementation while iterating on validation rules.

## References
- docs/backend/routes.md (V-Dem route expectations)
- src/controllers/vdem.controller.ts
- src/services/vdem.service.ts
