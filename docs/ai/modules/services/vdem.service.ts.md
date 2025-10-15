# Module: src/services/vdem.service.ts

- **Purpose**: Executes analytical queries against a V-Dem parquet dataset using DuckDB.
- **Key types/interfaces**: Internal `Row` interface captures dynamic column shapes returned from DuckDB.
- **Key functions/classes**: `queryVdemData` builds SQL with selected fields and country filters, applying default year bounds.
- **Side effects & state**: Instantiates a process-wide in-memory DuckDB database; reads parquet files from disk and throws `AppError` if missing.
- **Dependencies**: Depends on `duckdb`, Node `fs`/`path`, environment variables `VDEM_DATA_PATH`/`PARQUET_PATH`, and `utils/error` for exceptions.
- **How to extend**: Support pagination or aggregates by adjusting SQL; consider parameterized queries to mitigate injection risks; extract default year logic into config/env for flexibility.
