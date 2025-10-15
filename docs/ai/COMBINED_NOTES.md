# Combined Notes
## Logs

### logs/01-inventory.md

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

### logs/02-openapi-validation.md

# OpenAPI Validation

## npm run docs:lint
```
> api@1.0.0 docs:lint
> openapi-cli lint openapi/openapi.yaml

No configurations were provided -- using built in recommended configuration by default.

validating openapi/openapi.yaml...
openapi/openapi.yaml: validated in 42ms

Woohoo! Your API description is valid. 🎉
```

## npm run docs:bundle
```
> api@1.0.0 docs:bundle
> openapi-cli bundle openapi/openapi.yaml -o openapi/bundle.yaml

bundling openapi/openapi.yaml...
📦 Created a bundle for openapi/openapi.yaml at openapi/bundle.yaml 35ms.
```

## npm run docs:site
```
> api@1.0.0 docs:site
> redocly build-docs openapi/bundle.yaml -o openapi/index.html

Prerendering docs

🎉 bundled successfully in: openapi/index.html (126 KiB) [⏱ 3ms].
```

## Module Index

- `app.ts.md` — Constructs and configures the Express application, wiring global middleware and domain routers.
- `controllers/analysis.controller.ts.md` — Exposes analysis endpoints for generating correlation explanations and retrieving ranked correlations between datasets.
- `controllers/imf.controller.ts.md` — Handles IMF domain HTTP requests, providing health checks and normalized query responses.
- `controllers/vdem.controller.ts.md` — Serves V-Dem dataset routes, mapping HTTP requests to DuckDB-backed queries.
- `index.ts.md` — Bootstraps environment configuration and starts the Express application exported from `app.ts`.
- `routes/analysis.routes.ts.md` — Defines Express routing for analysis-related endpoints under `/analysis`.
- `routes/imf.routes.ts.md` — Exposes IMF dataset endpoints under the `/imf` prefix.
- `routes/vdem.routes.ts.md` — Provides V-Dem endpoints under the `/v-dem` namespace.
- `services/correlations.service.ts.md` — Provides correlation lookups against parquet datasets, both for specific index pairs and ranked lists by criteria.
- `services/helpers/prompt.ts.md` — Generates structured prompts for OpenAI explanations tying together metadata, correlation stats, and optional data series.
- `services/imf.service.ts.md` — Fetches and normalizes IMF indicator data via the IMF SDMX API, resolving country inputs and aggregating results.
- `services/index-meta.service.ts.md` — Loads and caches V-Dem index metadata from JSON into an easily queryable map.
- `services/metadata-universal.service.ts.md` — Resolves indicator metadata regardless of dataset origin by combining V-Dem metadata with IMF definition files.
- `services/openai.service.ts.md` — Provides a lazily instantiated OpenAI API client for use across the application.
- `services/vdem.service.ts.md` — Executes analytical queries against a V-Dem parquet dataset using DuckDB.
- `types/explain.ts.md` — Defines shared TypeScript types for metadata, explanation requests/responses, and correlation payloads.
- `types/request.ts.md` — Supplies shared request typings for controllers handling dataset queries.
- `utils/error.ts.md` — Centralizes error representation with an `AppError` class and normalizes thrown errors into HTTP-friendly payloads.
- `utils/ttlCache.ts.md` — Provides a simple in-memory TTL cache keyed by string for lightweight memoization.
