# Analysis Controller

**Source**: `src/controllers/analysis.controller.ts`

## Endpoints
- `POST /analysis/relationships/explain` – builds an explanation prompt and optionally executes it against OpenAI.
- `GET /analysis/relationships/datasets/correlations` – returns ranked correlation pairs for a country and dataset pairing.

## Explain Workflow
- Reads `ExplainRequest` payloads containing `indexA`, `indexB`, `country`, and optional `execute`.
- Resolves metadata through `getIndexMetaUniversal`, falling back to generic labels when definitions are missing.
- Retrieves correlation stats via `getCorrelation`. Missing parquet data yields a 404 with `correlation not found`.
- Builds structured prompts with `buildExplainPrompt`. When `execute` is true, invokes `getOpenAIClient` and caches results in `TTLCache`.
- Cache keys include indicator codes, country, correlation stats, prompt body, and model name; repeated requests return `{ cached: true }`.

## Correlation Listing Workflow
- Accepts query parameters `country`, `type`, `dataset1`, `dataset2`, `minObservations`, `limit`.
- Validates `type` against supported ranking modes and ensures datasets are among `VDEM`, `WEO`, or `NEA`.
- Cap the response at five records via the `limit` variable; the service handles sorting and filtering logic.

## Error Handling
- Missing parameters result in HTTP 400 responses with descriptive messages.
- Service-level exceptions fall back to generic HTTP 500 payloads containing the thrown message.
- OpenAI-related failures surface as 500 errors unless the cache already holds a previous successful response.
