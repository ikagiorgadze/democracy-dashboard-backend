# Module: src/services/metadata-universal.service.ts

- **Purpose**: Resolves indicator metadata regardless of dataset origin by combining V-Dem metadata with IMF definition files.
- **Key types/interfaces**: Returns `IndexMeta` objects defined in `types/explain`.
- **Key functions/classes**: `getIndexMetaUniversal` detects dataset type, fetches metadata from appropriate source, and normalizes fallbacks; helper `detectDatasetByShape` and `loadImfDefinitions` aid detection and caching.
- **Side effects & state**: Caches IMF definitions in-memory; reads JSON from disk using `fs/promises`.
- **Dependencies**: Depends on `services/index-meta.service` for V-Dem metadata, Node `path`, and dataset definition files in `data/definitions`.
- **How to extend**: Introduce additional dataset heuristics (e.g., NEA) and corresponding definition loaders; consider memoizing negative lookups or adding stale-cache invalidation hooks.
