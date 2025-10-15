# Correlation Service

**Source**: `src/services/correlations.service.ts`

## Responsibility
- Provide correlation statistics derived from parquet aggregates stored per country.
- Support both point lookups (`getCorrelation`) and ranked lists (`getTopCorrelations`).

## Data Expectations
- `CORRELATIONS_DIR` should point to a directory containing subfolders like `country_name=Kenya/part-*.parquet`.
- Each parquet row must contain `index_a`, `index_b`, `r_pearson`, `n_obs`, `p_value`, and `country_name`.

## `getCorrelation(indexA, indexB, country)`
- Normalizes indicator codes by stripping dataset prefixes (text before `:`) and lowercasing.
- Locates the matching country folder via exact or case-insensitive directory names.
- Uses DuckDB to query all parquet parts for matching pairs, considering both index orientations.
- Ranks candidate rows by observation count, absolute correlation strength, then p-value.
- Returns `{ r, n, p_value, method: 'Pearson' }` plus optional `yearsCovered`.

## `getTopCorrelations(country, type, dataset1, dataset2, minObservations?, limit)`
- Validates input enums before reading parquet files.
- Builds dynamic SQL to filter by dataset prefixes, absolute strength, p-value, or observation count depending on `type`.
- Limits the dataset to the provided `limit` (default 3, controller currently uses 5) and returns an array of `{ indexA, indexB, r, n, p_value }`.

## Operational Notes
- The module caches a single in-memory DuckDB instance; there is no connection pooling.
- Missing parquet folders return `null` (for single lookups) or an empty array (for rankings), allowing controllers to translate into HTTP status codes.
- Keep index naming consistent (e.g., `VDEM:code`, `WEO:code`) so dataset filters work as intended.
