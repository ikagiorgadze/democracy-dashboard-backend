# Module: src/services/imf.service.ts

- **Purpose**: Fetches and normalizes IMF indicator data via the IMF SDMX API, resolving country inputs and aggregating results.
- **Key types/interfaces**: `ImfQueryParams` for request parameters, `ImfResultRow` for aggregated output.
- **Key functions/classes**: `queryImfData` validates bounds, resolves country codes, orchestrates parallel series fetches, and merges observations; helper functions handle country map loading, URL building, API requests, and XML parsing.
- **Side effects & state**: Caches country code maps in-memory; performs outbound HTTP requests and throws `AppError` on failures or validation issues.
- **Dependencies**: Uses Node `fs`/`path`, global `fetch`, IMF metadata JSON under `data/definitions`, and `utils/error` for domain errors.
- **How to extend**: Add retry/backoff or circuit breaking to `fetchSingleSeries`; support batching across multiple IMF datasets by adjusting `buildImfUrl`; ensure new behaviors respect the caching strategy and maintain consistent `ImfResultRow` shape.
