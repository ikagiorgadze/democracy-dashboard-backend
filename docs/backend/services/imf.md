# IMF Service

**Source**: `src/services/imf.service.ts`

## Responsibility
- Translate incoming country identifiers into IMF SDMX codes, fetch indicator series, and shape observations into country/year rows.

## Input & Output
- `queryImfData({ countries, fields, start_year, end_year, isNea })` accepts human or ISO country identifiers and SDMX series codes.
- Returns an array sorted by `country_name`, `year`, with indicator values (numbers or `null`) per requested code.
- Throws `AppError` for validation issues (e.g., `end_year < start_year`) or network problems.

## Implementation Highlights
- Lazily loads country code maps from `data/definitions/imf/series-code-countries.json`, caching forward and reverse lookups.
- Resolves fuzzy country names by substring match when exact codes are absent.
- Builds SDMX URLs with optional ANEA dataset support via `isNea`.
- Issues parallel fetches with `Promise.all`. Each response is parsed with regex-based XML extraction tailored to `<Series>` and `<Obs>` nodes.
- Aggregates results into a `Map` keyed by `country|year`, ensuring multi-series merges retain shared rows.

## Configuration
- `IMF_MAX_FIELDS` caps the number of series per request (enforced in the controller).
- `IMF_TIMEOUT_MS` controls the AbortController timeout for each network call (default 15 seconds).
- Ensure outbound network access to `https://api.imf.org` in deployed environments.

## Maintenance Tips
- Refresh the country mapping JSON whenever IMF releases new codes.
- Consider persisting results or adding retries if rate limits become apparent in production.
