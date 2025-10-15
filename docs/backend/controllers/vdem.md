# V-Dem Controller

**Source**: `src/controllers/vdem.controller.ts`

## Endpoints
- `GET /v-dem/health` – returns `{ ok: true, service: 'v-dem' }` for uptime checks.
- `POST /v-dem/query` – accepts a JSON body with `countries`, `fields`, optional `start_year`, `end_year`.

## Behaviour
- Validates that a JSON payload is present before invoking the service.
- Delegates to `queryVdemData` (`services/vdem.service.ts`) to run DuckDB parquet queries.
- Converts BigInt values in the service response to plain numbers to avoid JSON serialization issues.
- Errors fall back to `formatError`, ensuring consistent `{ error, status }` responses.

## Usage Notes
- Expect arrays of indicator values keyed by the requested field names in the response.
- The controller does not sanitize country names; ensure upstream callers submit trusted ISO names matching the parquet.
- If the parquet file is missing, the service throws an `AppError` that surfaces here as a 500 with a descriptive message.
