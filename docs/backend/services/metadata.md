# Metadata Services

**Sources**
- `src/services/index-meta.service.ts`
- `src/services/metadata-universal.service.ts`

## V-Dem Index Metadata
- `loadIndexMeta(filePath?, forceReload?)` reads `data/definitions/vdem/index-details.json`, producing a `Map<string, IndexMeta>` keyed by lowercase indicator codes.
- Results are cached in-memory; pass `forceReload: true` (used automatically in development) to pick up on-disk changes.
- `getIndexMetaSafe(code)` wraps the loader and returns `null` when no definition is found.

## Universal Lookup
- `getIndexMetaUniversal(code)` detects dataset type heuristically: IMF series include a dot (`.`), while others default to V-Dem.
- V-Dem codes delegate to `getIndexMetaSafe`, falling back to generic labels when the JSON lacks an entry.
- IMF codes consult `data/definitions/imf/imf-series-code-definitions.json`, returning the provided short description as both name and definition.
- Returns an `IndexMeta` shape for any code, ensuring the explanation endpoint can always build a prompt.

## Usage Notes
- Place definition files under `data/definitions/...` or update environment variables to point elsewhere before booting.
- IMF lookups cache the parsed JSON in a `Map`; restart the process after updating the definitions to refresh the cache.
- The universal helper intentionally avoids throwing when metadata is missing, allowing controllers to produce usable fallbacks.
