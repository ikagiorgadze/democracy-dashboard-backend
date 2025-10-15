# Democracy Dashboard Backend Documentation

## Overview
The Democracy Dashboard backend is a Node.js/Express service that aggregates democracy-related datasets (V-Dem parquet files, IMF SDMX API) and delivers analytical endpoints for correlation insights and automated narratives. The codebase favors a classic controller/service structure with thin routers and reusable utilities.

## Architecture
```
[Client]
   |
   v
[Express App (src/app.ts)]
   |
   +-- Routers (/v-dem, /imf, /analysis)
         |
         +-- Controllers
                |
                +-- Services (DuckDB, IMF SDMX fetch, metadata, LLM client)
                        |
                        +-- Data files (data/parquets, data/definitions)
                        +-- External APIs (IMF, OpenAI)
```

- `src/index.ts` bootstraps environment variables and starts the HTTP listener.
- Controllers enforce validation and transform service outputs into HTTP responses.
- Services encapsulate DuckDB parquet queries, IMF network calls, metadata loading, and language-model prompt execution.
- Utilities provide shared error handling and an in-memory TTL cache for expensive explain responses.

## Setup & Local Development
1. Install dependencies: `npm install`
2. Prepare environment variables (example `.env`):
   ```bash
   PORT=3000
   CORS_ORIGIN=http://localhost:5173
   VDEM_DATA_PATH=data/parquets/vdem_data.parquet
   CORRELATIONS_DIR=data/parquets/correlations
   OPENAI_API_KEY=sk-...
   IMF_MAX_FIELDS=5
   IMF_TIMEOUT_MS=15000
   EXPLAIN_CACHE_TTL_MS=21600000
   ```
3. Ensure required data assets exist:
   - V-Dem parquet: `data/parquets/vdem_data.parquet`
   - Correlation parquet folders: `data/parquets/correlations/country_name=<Country>`
   - Metadata definitions: `data/definitions/vdem/index-details.json`, IMF definitions under `data/definitions/imf/`
4. Run in watch mode: `npm run dev`
5. Health checks: `GET http://localhost:3000/health`

## Deployment Notes
- Build TypeScript to JavaScript with `npm run build` and run via `npm start`.
- Ensure environment variables above are set in the hosting environment; secrets (e.g., `OPENAI_API_KEY`) should be stored securely.
- Mount parquet and JSON definition files on the filesystem accessible to the app or configure `VDEM_DATA_PATH` and `CORRELATIONS_DIR` to point to mounted volumes.
- Outbound network access must allow IMF SDMX API and OpenAI endpoints when `execute` mode is used.

## API Reference
Full reference: [OpenAPI HTML](../openapi/index.html)

| Method | Path | Description |
| --- | --- | --- |
| GET | `/health` | Global service heartbeat. |
| GET | `/v-dem/health` | V-Dem service heartbeat. |
| POST | `/v-dem/query` | Query V-Dem parquet data by countries, fields, and optional years. |
| GET | `/imf/health` | IMF service heartbeat. |
| POST | `/imf/query` | Fetch IMF indicators for selected countries and series. |
| POST | `/analysis/relationships/explain` | Generate or preview a correlation explanation narrative. |
| GET | `/analysis/relationships/datasets/correlations` | Retrieve ranked correlations across datasets for a country. |

## Data Model Overview
- **BaseQueryPayload** (`countries[]`, `fields[]`, `start_year?`, `end_year?`, `isNea?`) underpins dataset query endpoints.
- **V-Dem Rows**: objects with `country_name`, `year`, plus dynamic indicator columns returned directly from parquet files.
- **IMF Rows**: objects with `country_name`, `year`, and selected indicator codes mapped to numeric values (or null when missing).
- **Correlation Pair**: items include `indexA`, `indexB`, Pearson `r`, observation count `n`, and `p_value` pulled from parquet aggregates.
- **Explain Workflow**: accepts `Index` (`name`, optional `data[]`) and returns either a structured prompt context or an executed explanation along with cache metadata.

## Known Limitations
- Request validation relies on manual checks; a schema validator would improve error consistency.
- DuckDB queries build SQL via string interpolation—inputs should remain trusted to avoid injection risks.
- IMF requests are sequential per indicator; high field counts increase latency.
- Explanation responses depend on OpenAI availability and correct API key configuration.
- Health endpoints lack detailed status of downstream dependencies.

## Glossary
- **DuckDB**: In-process analytical database used to query parquet files.
- **V-Dem**: Varieties of Democracy dataset providing democratic indicators.
- **IMF SDMX**: International Monetary Fund Statistical Data and Metadata eXchange API.
- **Correlation Pair**: Aggregated statistic describing co-movement between indicators.
- **TTL Cache**: Time-to-live cache storing computed explanations to reduce repeated OpenAI calls.

## Changelog Seed
- `2025-01-xx` – Initial documentation pass, OpenAPI spec, and Redoc site generation.
- Future entries should capture API changes, dataset schema updates, and integration milestones.
