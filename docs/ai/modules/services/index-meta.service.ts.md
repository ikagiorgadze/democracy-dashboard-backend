# Module: src/services/index-meta.service.ts

- **Purpose**: Loads and caches V-Dem index metadata from JSON into an easily queryable map.
- **Key types/interfaces**: Utilizes the `IndexMeta` type from `types/explain`.
- **Key functions/classes**: `loadIndexMeta` reads and parses the metadata file, populating a cached `Map`; `getIndexMetaSafe` retrieves entries with optional dev-time cache busting.
- **Side effects & state**: Maintains an in-memory cache of metadata; reads from filesystem path `data/definitions/vdem/index-details.json`.
- **Dependencies**: Node `fs/promises`, `path`, and environment variable `NODE_ENV` for cache invalidation behavior.
- **How to extend**: Support alternative data sources by parameterizing the base path, or add validation to enforce metadata schema; consider exposing batch retrieval helpers for bulk operations.
