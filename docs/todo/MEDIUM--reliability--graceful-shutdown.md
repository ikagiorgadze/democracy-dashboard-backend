---
id: graceful-shutdown
title: "Implement graceful shutdown"
severity: MEDIUM
type: reliability
component: "src/index.ts"
status: open
estimate: "S"
dependencies: []
---

## Rationale
`src/index.ts` starts the HTTP listener but never stores the server handle or listens for termination signals. Production environments (systemd, containers) commonly send `SIGTERM` or `SIGINT` during deployment. Without graceful shutdown, in-flight requests may be dropped, and DuckDB/OpenAI resources are left hanging. The operations guide mentions deployment but omits shutdown handling, so we must harden it.

## Evidence
- src/index.ts:4-6
  ```ts
  const PORT = process.env.PORT || 3000;
  app.listen(PORT, () => {
  });
  ```
- No signal handling or `server.close()` logic elsewhere in the codebase.

## Impact
Rolling deploys can terminate the process immediately, leading to failed requests and potential data corruption (e.g., partially written cache). Without logging on shutdown, operators lose visibility into restart loops.

## Proposed Change
Capture the server instance returned by `app.listen`, add listeners for `SIGINT`/`SIGTERM`, stop accepting new connections, and wait for existing ones to complete before exiting. Log shutdown events to aid observability. Consider a timeout to force exit if connections hang.

## Acceptance Criteria
- [ ] Process handles termination signals and closes the HTTP server gracefully.
- [ ] Shutdown path logs start/end and exits with appropriate status codes.
- [ ] Tests (or manual verification) demonstrate graceful behavior.
- [ ] Operations docs describe the shutdown process.

## Tasks
- [ ] Update `index.ts` to retain the server handle and add signal handlers.
- [ ] Add logging for startup and shutdown events.
- [ ] Document the new behavior in docs/backend/operations.md.
- [ ] Optionally expose a helper for future resource cleanup (e.g., DuckDB connections).

## Rollout & Risk
Low risk; only affects shutdown behavior. Verify in staging by sending SIGTERM and observing logs/responses before deploying widely.

## References
- src/index.ts
- docs/backend/operations.md
