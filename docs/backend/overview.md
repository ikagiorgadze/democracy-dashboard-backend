# Backend Overview

## Architecture
- Express 5 application written in TypeScript (`src/app.ts`) and started from `src/index.ts`.
- Modular routing (`src/routes`) forwards requests to thin controllers under `src/controllers`.
- Controllers delegate IO and business logic to services in `src/services`, keeping HTTP concerns separate from data work.
- DuckDB provides parquet access for V-Dem and correlation datasets; IMF data comes from the IMF SDMX API.
- OpenAI chat completions supply optional narrative explanations for correlation results.

```
Request → Express router → Controller → Service → (DuckDB | IMF API | OpenAI) → Response
```

## Runtime Flow
- `src/index.ts` loads environment variables, constructs the Express app, and begins listening on `PORT`.
- `src/app.ts` configures CORS, JSON parsing, and mounts routers at `/v-dem`, `/imf`, and `/analysis`.
- Health checks exist at `/health`, `/v-dem/health`, and `/imf/health`; each controller returns a simple JSON payload.
- Error handling relies on `AppError` plus the `formatError` helper to keep responses consistent.
- Narratives generated through `/analysis/relationships/explain` can be cached in-memory via `TTLCache` to avoid duplicate OpenAI calls.

## Responsibilities by Domain
- **V-Dem**: filter parquet-backed indicator data for a set of countries and years.
- **IMF**: normalize country codes, fetch SDMX XML responses, and reshape observations into tidy series.
- **Correlation analysis**: look up aggregated parquet statistics, rank correlation pairs, and provide metadata for narrative explanations.
- **Metadata services**: combine V-Dem JSON definitions with IMF indicator descriptions to provide user-facing labels.

## Key Environment Variables
- `PORT`, `CORS_ORIGIN`, `VDEM_DATA_PATH`, `CORRELATIONS_DIR` configure server basics and data locations.
- `IMF_MAX_FIELDS`, `IMF_TIMEOUT_MS` guard IMF API usage.
- `OPENAI_API_KEY`, `OPENAI_MODEL`, and `EXPLAIN_CACHE_TTL_MS` drive narrative generation behavior.

## Additional References
- [Route Map](routes.md)
- [Data Assets](data-assets.md)
- [Operations Guide](operations.md)
