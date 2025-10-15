# IMF Controller

**Source**: `src/controllers/imf.controller.ts`

## Endpoints
- `GET /imf/health` – simple heartbeat for monitoring.
- `POST /imf/query` – fetches IMF indicators for supplied countries and series.

## Behaviour
- Requires a JSON body; throws an `AppError` if the payload is missing or malformed.
- Validates `countries`, `fields`, `start_year`, and `end_year`. Rejects empty arrays and enforces numeric year bounds.
- Enforces the `IMF_MAX_FIELDS` limit (defaults to `5` if not provided) to keep SDMX calls manageable.
- Delegates to `queryImfData` (`services/imf.service.ts`), which performs country code resolution and API fetches.
- Returns the service output verbatim; each row represents a country-year with indicator values.

## Usage Notes
- Expect HTTP 400 for validation errors (missing countries, too many fields, invalid years).
- Network timeouts bubble up from the service as `AppError` instances with status 504.
- The controller stays stateless; caching and rate limiting must be implemented elsewhere if required.
