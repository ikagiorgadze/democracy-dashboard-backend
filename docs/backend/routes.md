# Route Map

| Method | Path | Controller | Service Layer | Notes |
| --- | --- | --- | --- | --- |
| GET | `/health` | Inline in `src/app.ts` | — | Global heartbeat used by deploy probes. |
| GET | `/v-dem/health` | `getHealth` (`controllers/vdem.controller.ts`) | — | Confirms parquet access is wired; returns `{ ok: true, service: 'v-dem' }`. |
| POST | `/v-dem/query` | `queryVdemDataController` (`controllers/vdem.controller.ts`) | `queryVdemData` (`services/vdem.service.ts`) | Filters the V-Dem parquet by countries, years, and requested indicators. |
| GET | `/imf/health` | `getHealth` (`controllers/imf.controller.ts`) | — | IMF subsystem heartbeat. |
| POST | `/imf/query` | `queryImfData` (`controllers/imf.controller.ts`) | `queryImfData` (`services/imf.service.ts`) | Resolves country codes, fetches SDMX XML for each indicator, and reshapes observations. |
| POST | `/analysis/relationships/explain` | `explainRelationshipsController` (`controllers/analysis.controller.ts`) | `getIndexMetaUniversal`, `getCorrelation`, `buildExplainPrompt`, `getOpenAIClient` | Builds or executes a correlation explanation narrative; optional OpenAI call when `execute` is true. |
| GET | `/analysis/relationships/datasets/correlations` | `getCorrelationsController` (`controllers/analysis.controller.ts`) | `getTopCorrelations` (`services/correlations.service.ts`) | Returns ranked correlation pairs between dataset combinations for a country. |

## Notes
- Routers live in `src/routes/*.routes.ts`, keeping mounting logic separate from handler implementations.
- All routes expect JSON payloads and benefit from the global `express.json()` middleware configured in `src/app.ts`.
- Controllers convert thrown `AppError` instances into HTTP status codes using `formatError`.
