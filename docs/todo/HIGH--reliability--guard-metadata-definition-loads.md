---
id: metadata-definition-guard
title: "Guard IMF metadata definition loads"
severity: HIGH
type: reliability
component: "src/services/metadata-universal.service.ts"
status: open
estimate: "S"
dependencies: []
---

## Rationale
The universal metadata service reads `data/definitions/imf/imf-series-code-definitions.json` on demand without checking whether the file exists or handling read failures. The docs call out that these definition files must be “mounted” in each environment, so absence is a likely deployment misconfiguration. Today the first IMF metadata lookup throws an unhandled `ENOENT`, cascading into a 500 for `/analysis/relationships/explain`. We need graceful degradation and actionable errors so the app remains operable while misconfigurations are corrected.

## Evidence
- src/services/metadata-universal.service.ts:16-27
  ```ts
  const file = path.resolve(process.cwd(), 'data/definitions/imf/imf-series-code-definitions.json');
  const raw = await fs.readFile(file, 'utf-8');
  const obj = JSON.parse(raw) as Record<string, string>;
  ```
- No try/catch, no fallback when the file is missing or malformed.

## Impact
Missing or unreadable definition files cause every explanation request that hits IMF indicators to crash, breaking narratives for those datasets and hiding the root cause. This turns a configuration slip into a runtime outage, contradicting the operations guide’s expectation of reliable diagnostics.

## Proposed Change
Wrap file access in defensive logic: check existence, surface a clear `AppError` (e.g., 503 with actionable message), and allow the rest of the request to continue with a safe fallback (generic metadata) when definitions are unavailable. Add logging so operators can detect missing assets quickly.

## Acceptance Criteria
- [ ] IMF definition loads handle ENOENT/parse errors without throwing uncaught exceptions.
- [ ] Controllers receive structured errors (status + detail) that translate to helpful client responses.
- [ ] Tests cover missing-file and malformed JSON scenarios.
- [ ] Operations documentation gains troubleshooting steps for absent metadata mounts.

## Tasks
- [ ] Add guarded file reads (try/catch) and surface AppError with remediation hints.
- [ ] Provide fallback metadata for IMF indicators when definitions cannot be loaded.
- [ ] Write unit tests for both success and failure paths.
- [ ] Update docs/backend/data-assets.md with failure troubleshooting.

## Rollout & Risk
Low risk; the change only adds guards and clearer errors. Verify by running the analysis endpoint with the file removed to ensure a controlled 503 response before deploying.

## References
- docs/backend/data-assets.md
- src/services/metadata-universal.service.ts
- docs/backend/operations.md
