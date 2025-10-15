# Module: src/utils/ttlCache.ts

- **Purpose**: Provides a simple in-memory TTL cache keyed by string for lightweight memoization.
- **Key types/interfaces**: Generic `TTLCache<V>` class storing values with expiration metadata.
- **Key functions/classes**: Methods `get`, `set`, and `clear` manage cached entries and enforce capacity/expiry rules.
- **Side effects & state**: Maintains state in an internal `Map`; evicts oldest entries when reaching `maxSize`.
- **Dependencies**: None beyond standard JavaScript runtime.
- **How to extend**: Add async variants or expose metrics (hit/miss counts); consider hooking into external storage if persistence is needed.
