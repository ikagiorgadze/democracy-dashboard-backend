---
id: express-error-middleware
title: "Install centralized error middleware"
severity: MEDIUM
type: architecture
component: "src/app.ts"
status: open
estimate: "S"
dependencies: []
---

## Rationale
Beyond per-controller try/catch blocks, the app lacks a global Express error handler. When controllers throw (e.g., due to validation bugs or unexpected errors), Express responds with its default HTML stack trace rather than the JSON structure defined by `formatError`. The documentation emphasizes reusable utilities for error handling, yet without middleware the utility cannot be applied consistently across routes.

## Evidence
- src/app.ts – no `app.use((err, req, res, next) => …)` is registered after the routers.
- src/utils/error.ts defines `formatError` but nothing wires it into Express globally.
- src/controllers/analysis.controller.ts catch block returns plain `res.status(500).json({ error: 'internal error', details: … })`, which diverges from other controllers and underscores the need for a shared path.

## Impact
Unhandled errors leak HTML to clients, complicate frontend handling, and risk exposing stack traces in production. Without a single middleware, each controller must duplicate error handling, increasing drift and maintenance cost.

## Proposed Change
Add a final middleware that converts unknown errors into JSON using `formatError`, ensuring consistent status codes and response bodies. Controllers can then throw `AppError` or rely on async error propagation instead of wrapping every call.

## Acceptance Criteria
- [ ] Express registers a single error-handling middleware after route mounting.
- [ ] Uncaught errors return JSON with `{ error, status, details? }`.
- [ ] Existing controllers can remove redundant try/catch blocks where appropriate.
- [ ] Tests verify that throwing an `AppError` and a generic `Error` yield expected status codes.

## Tasks
- [ ] Implement middleware reusing `formatError`.
- [ ] Refactor controllers to rely on it (keep domain-specific catches only where necessary).
- [ ] Add integration tests hitting `/v-dem/query` with malformed payloads to confirm responses.
- [ ] Document the middleware in docs/backend/utilities/error.md.

## Rollout & Risk
Low risk; only affects error responses. Deploy to staging to confirm no HTML leaks remain and that clients handle the JSON format as expected.

## References
- src/app.ts
- src/utils/error.ts
- docs/backend/utilities/error.md
