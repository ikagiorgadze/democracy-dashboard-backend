---
id: structured-logging
title: "Introduce structured request and error logging"
severity: MEDIUM
type: architecture
component: "src/app.ts"
status: open
estimate: "M"
dependencies: []
---

## Rationale
The API has no request or error logging; a repo-wide search shows no `console` usage or logging library. Without middleware to capture inbound requests, response status, latency, and error details, operators lack visibility into failures. This contradicts the requirement that all endpoints have proper error handling and diagnostics.

## Evidence
- `rg "console" -n src` returns no matches, confirming no logging.
- src/app.ts only wires `cors` and `express.json()`; no request logging or structured error logging middleware is present.
- Error middleware TODO exists, but logging is still missing, leaving no telemetry when errors occur.

## Impact
Production issues cannot be triaged quickly because there is no trace of who called an endpoint, what payload was sent, or how the handler responded. Security incidents (e.g., injection attempts) go unnoticed without logs, and the team cannot correlate OpenAI/IMF failures with API usage.

## Proposed Change
Adopt structured logging (pino, winston, or similar) with middleware that records request/response metadata and hooks into the error handler. Emit JSON logs suitable for aggregation, include correlation IDs, and ensure sensitive fields are redacted.

## Acceptance Criteria
- [ ] Logging middleware captures at least method, path, status, duration, and request ID for each request.
- [ ] Error handling middleware emits structured logs for failures, including stack traces and sanitized details.
- [ ] Logs avoid leaking secrets (strip Authorization headers, API keys).
- [ ] Documentation explains logging configuration and how to adjust log levels per environment.

## Tasks
- [ ] Choose a logging library and add dependency.
- [ ] Implement request/response logging middleware with optional correlation IDs.
- [ ] Integrate logging into the centralized error handler.
- [ ] Update operations docs with log ingestion guidance.

## Rollout & Risk
Moderate; ensure log volume is acceptable before production rollout. Test locally and in staging to confirm formatting and redaction rules.

## References
- src/app.ts
- docs/backend/operations.md
