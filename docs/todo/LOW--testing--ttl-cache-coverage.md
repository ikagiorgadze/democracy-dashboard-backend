---
id: tests-ttl-cache
title: "Add unit tests for TTL cache utility"
severity: LOW
type: testing
component: "src/utils/ttlCache.ts"
status: open
estimate: "S"
dependencies: ["testing-foundation"]
---

## Rationale
The TTL cache underpins the explain endpoint’s memoization. It has no automated tests despite handling expiration logic and capacity eviction. Simple unit coverage will guard against regressions when tweaking cache behavior (e.g., TTL configuration, eviction strategy).

## Evidence
- src/utils/ttlCache.ts – implements `get`, `set`, `clear` without tests.
- No `*.test.ts` files exist in the repo (`rg --files` check).

## Impact
Changes to the cache (increasing TTL, adjusting eviction) risk breaking memoization or leaving stale entries if expiration fails. Bugs here waste OpenAI calls or serve outdated narratives.

## Proposed Change
Add lightweight unit tests verifying TTL expiration, eviction when max size is reached, and `clear` behavior.

## Acceptance Criteria
- [ ] Tests confirm expired entries are evicted on `get`.
- [ ] Tests confirm FIFO eviction when capacity is exceeded.
- [ ] Tests cover `clear` resetting the store.
- [ ] Tests run quickly without external dependencies.

## Tasks
- [ ] Write unit tests with mocked timers to simulate expiration.
- [ ] Verify eviction order with a small cache size.
- [ ] Add documentation note referencing the new tests.

## Rollout & Risk
Negligible risk; purely additive. Run tests locally/CI.

## References
- src/utils/ttlCache.ts
- docs/backend/utilities/ttl-cache.md
