# Operations Guide

## Local Development
- Install dependencies with `npm install`.
- Run `npm run dev` to start the TypeScript watcher (`tsx watch src/index.ts`); the server reloads on file changes.
- Populate `.env` with required settings (`PORT`, `CORS_ORIGIN`, `VDEM_DATA_PATH`, `CORRELATIONS_DIR`, `IMF_MAX_FIELDS`, `OPENAI_API_KEY`, etc.). The code reads environment variables before the app boots.
- Place parquet and definition files in the expected locations or point environment variables at mounted paths (see [Data Assets](data-assets.md)).

## Builds & Deployment
- Compile TypeScript using `npm run build`; the output lands in `dist/`.
- Start the compiled build with `npm start` (`node dist/index.js`).
- Keep `OPENAI_API_KEY` and other secrets out of source control; set them via the hosting platform.
- Health checks should target `/health` for the whole service and `/v-dem/health` / `/imf/health` for domain-specific probes.

## Diagnostics & Utilities
- `npm run docs:lint`, `npm run docs:bundle`, and `npm run docs:site` manage the OpenAPI assets under `openapi/`.
- Development helpers in `package.json` (e.g., `npm run dev:inspect-parquet`, `npm run dev:test-corr`) rely on paths under `src/dev/`; supply the same environment variables as the main app.
- Logging currently uses `console.*` statements; integrate a structured logger if deployment requires richer telemetry.

## Verification Checklist
- Before deploying, confirm DuckDB can read the configured parquet files by running a sample `/v-dem/query` request.
- Ensure IMF requests succeed for at least one indicator and that `IMF_MAX_FIELDS` matches operational expectations.
- When enabling explanation execution, verify the cache TTL via `EXPLAIN_CACHE_TTL_MS` and confirm the OpenAI API key is valid.
