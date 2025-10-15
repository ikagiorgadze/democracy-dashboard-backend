# V-Dem Service

**Source**: `src/services/vdem.service.ts`

## Responsibility
- Query V-Dem parquet data via DuckDB and return tabular results keyed by indicator names.
- Normalize file paths based on environment variables to support both local and deployed setups.

## Input & Output
- `queryVdemData(countries, fields, startYear?, endYear?)` expects ISO-style `country_name` strings and indicator column names.
- Returns an array of objects: `{ country_name, year, <indicator>: value }`.
- Throws `AppError` when the parquet file cannot be located.

## Implementation Notes
- Instantiates an in-memory DuckDB database and issues SQL against `read_parquet(...)`.
- Builds the projection list dynamically from the requested fields to avoid over-fetching.
- Defaults to a five-year window (`startYear` or 2000 through `startYear + 5`) when bounds are omitted.
- Performs no sanitation of field names; upstream validation should ensure trusted input.

## Configuration
- Preferred parquet path order: `VDEM_DATA_PATH` → `PARQUET_PATH` → `data/parquets/vdem_data.parquet`.
- Keep the working directory consistent with the repository root so `process.cwd()` resolves as expected.
