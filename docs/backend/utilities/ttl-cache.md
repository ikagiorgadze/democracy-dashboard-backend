# TTL Cache

**Source**: `src/utils/ttlCache.ts`

## Purpose
- Provide a lightweight in-memory cache with time-based eviction to avoid repeated costly operations (currently used for OpenAI explanations).

## API
- `new TTLCache(maxSize = 500, ttlMs = 6h)` – creates a cache with capacity and expiration settings.
- `get(key)` – returns cached value if it exists and has not expired; otherwise removes stale entries and returns `undefined`.
- `set(key, value)` – stores a value, evicting the oldest entry when capacity is reached.
- `clear()` – empties the cache.

## Operational Notes
- The cache is in-process; restart the server to flush entries across deployments.
- Configure `EXPLAIN_CACHE_TTL_MS` to align TTL with backend workload. The controller passes this value to the constructor.
- Suitable for small datasets; consider external stores (Redis, etc.) if clustering or persistence is required.
