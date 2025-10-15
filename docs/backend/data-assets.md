# Data Assets

## Parquet Sources
- **V-Dem indicators**: defaults to `data/parquets/vdem_data.parquet`. Override via `VDEM_DATA_PATH` (or legacy `PARQUET_PATH`). File must include `country_name`, `year`, and the requested indicator columns.
- **Correlation aggregates**: expected under `data/parquets/correlations/` with per-country folders such as `country_name=Sweden/part-*.parquet`. Point `CORRELATIONS_DIR` to a mounted volume when deploying.

## Definition Files
- **V-Dem index metadata**: `data/definitions/vdem/index-details.json` provides human-friendly names, questions, and definitions consumed by `index-meta.service`.
- **IMF indicator descriptions**: `data/definitions/imf/imf-series-code-definitions.json` maps SDMX codes to titles. Country code mappings live in `data/definitions/imf/series-code-countries.json`.

## Runtime Expectations
- Services resolve all paths relative to `process.cwd()`, so keep the working directory at the repository root or adjust environment variables accordingly.
- Missing files trigger clear `AppError` messages; add these assets before running `npm run dev` to avoid 500 responses.
- IMF queries require outbound HTTPS access to `api.imf.org` and may need `IMF_TIMEOUT_MS` tuning if the dataset grows.

## Maintenance Tips
- Refresh parquet partitions when new correlations are computed to keep `/analysis` endpoints in sync with the latest data.
- Update JSON definition files whenever indicator labels change, then reload the server or call `loadIndexMeta(..., true)` during development to pick up edits.
- Store large parquet datasets outside of the repo and mount them at runtime; avoid committing production-scale data.
