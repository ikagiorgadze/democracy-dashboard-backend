# Inventory

## Entry Points & Server Setup
- `src/index.ts`: loads env vars and starts Express app on `process.env.PORT || 3000`.
- `src/app.ts`: configures Express with CORS, JSON parsing, health endpoint, and mounts domain routers under `/v-dem`, `/imf`, `/analysis`.

## Routing
- `src/routes/vdem.routes.ts`: `/v-dem/health`, `/v-dem/query` (POST) delegating to V-Dem controllers.
- `src/routes/imf.routes.ts`: `/imf/health`, `/imf/query` (POST) delegating to IMF controllers.
- `src/routes/analysis.routes.ts`: `/analysis/relationships/explain` (POST) and `/analysis/relationships/datasets/correlations` (GET) for analytical features.

## Controllers
- `src/controllers/vdem.controller.ts`: health responder and query handler validating request body and calling V-Dem service with DuckDB backend.
- `src/controllers/imf.controller.ts`: health responder and query handler performing payload validation, enforcing field limits, and calling IMF service.
- `src/controllers/analysis.controller.ts`: orchestrates metadata lookup, correlation fetching, OpenAI prompt/execution, and exposes correlation listing endpoint.

## Services
- `src/services/vdem.service.ts`: DuckDB parquet querying for V-Dem data with dynamic parquet resolution and defaults.
- `src/services/imf.service.ts`: IMF API client with country-code resolution, XML parsing, and result aggregation.
- `src/services/correlations.service.ts`: DuckDB-powered parquet queries for correlation metrics and top-correlation ranking.
- `src/services/metadata-universal.service.ts`: merges V-Dem metadata with IMF definitions, auto-detects dataset source.
- `src/services/index-meta.service.ts`: loads and caches V-Dem metadata JSON.
- `src/services/openai.service.ts`: lazy OpenAI SDK client factory.
- `src/services/helpers/prompt.ts`: OpenAI prompt construction for explanation requests.

## Types & Utilities
- `src/types/explain.ts`: shared DTOs for explain/correlation flows, metadata shapes.
- `src/types/request.ts`: base request payload definition shared across controllers.
- `src/utils/error.ts`: `AppError` and normalized error formatter for HTTP responses.
- `src/utils/ttlCache.ts`: generic TTL cache used for explanation memoization.

## Data & External Dependencies
- Relies on parquet files under `data/parquets/**` for V-Dem/correlation data.
- Uses `data/definitions/**` JSON for IMF/V-Dem metadata enrichments.
- Requires `OPENAI_API_KEY` for explanation execution; optional `CORRELATIONS_DIR`, `VDEM_DATA_PATH`, `PARQUET_PATH`, `IMF_MAX_FIELDS`, `IMF_TIMEOUT_MS`, `EXPLAIN_CACHE_TTL_MS` env vars.

## Middleware & Error Handling
- Global middleware: CORS (configurable origin) and JSON body parsing in `app.ts`.
- Controllers convert thrown errors via `formatError` ensuring HTTP status propagation.

## Outstanding TODOs / Notes
- TODO comments in `app.ts` for Helmet and refined CORS configuration.
- No centralized error middleware; controllers respond manually on error.
