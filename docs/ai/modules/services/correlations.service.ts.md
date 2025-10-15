# Module: src/services/correlations.service.ts

- **Purpose**: Provides correlation lookups against parquet datasets, both for specific index pairs and ranked lists by criteria.
- **Key types/interfaces**: Uses `Correlation` and `CorrelationPair` from `types/explain`.
- **Key functions/classes**: `getCorrelation` locates per-country parquet partitions and retrieves the best-matching correlation row; `getTopCorrelations` returns ranked correlation pairs based on requested ordering.
- **Side effects & state**: Maintains a singleton in-memory DuckDB database; reads parquet files from disk; no persistent mutation beyond caching the DB handle.
- **Dependencies**: Relies on `duckdb`, Node `fs/promises` and `path`, environment variable `CORRELATIONS_DIR`, and `types/explain` definitions.
- **How to extend**: Add memoization for directory lookups, expose pagination for larger result sets, or parameterize correlation thresholds; ensure SQL remains injection-safe by sanitizing dynamic inputs.
