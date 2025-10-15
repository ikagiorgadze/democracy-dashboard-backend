---
id: testing-foundation
title: "Establish automated test suite"
severity: MEDIUM
type: testing
component: "repo"
status: open
estimate: "L"
dependencies: []
---

## Rationale
There is no automated test scaffolding (no `tests/`, no Jest/Vitest configs, no CI scripts) despite the code handling complex I/O (DuckDB, IMF API, OpenAI). Documentation and package.json scripts focus on manual dev helpers. Without tests, regressions slip through and hardening tasks (like the new validation work) remain unverified.

## Evidence
- `rg --files` shows no test directories or `.spec.ts` files.
- `package.json` lacks `test`/`lint` scripts beyond docs tooling.
- Docs do not describe any testing strategy.

## Impact
Engineers must manually exercise endpoints, slowing development and reducing confidence in refactors. Security and reliability fixes (like parameterizing queries) lack regression coverage, risking reintroduction of vulnerabilities.

## Proposed Change
Introduce a testing framework (e.g., Vitest or Jest) with TypeScript support, configure mocks for DuckDB/OpenAI, and write unit/integration tests around controllers and services. Add `npm test` to CI once available.

## Acceptance Criteria
- [ ] Test framework configured and runnable via `npm test`.
- [ ] Core services/controllers have coverage for success and failure paths.
- [ ] CI (or local pre-commit) runs the test suite.
- [ ] Docs include instructions for running tests locally.

## Tasks
- [ ] Select and configure testing stack (e.g., Vitest + supertest).
- [ ] Add fixtures/mocks for DuckDB and external APIs.
- [ ] Write initial tests covering V-Dem, IMF, and analysis controllers.
- [ ] Document testing workflow in README/operations guide.

## Rollout & Risk
Purely additive; start with a small suite and expand alongside upcoming fixes. CI integration can follow once the suite is stable.

## References
- package.json
- docs/backend/operations.md
