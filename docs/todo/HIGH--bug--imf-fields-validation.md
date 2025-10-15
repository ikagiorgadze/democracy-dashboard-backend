---
id: imf-fields-null-guard
title: "Fix IMF request field validation"
severity: HIGH
type: bug
component: "src/controllers/imf.controller.ts"
status: open
estimate: "S"
dependencies: ["request-schema-validation"]
---

## Rationale
`queryImfData` assumes the incoming payload includes a `fields` array, then accesses `fields.length` before confirming the value exists or is an array. When clients omit `fields` or send a non-array, the controller throws a `TypeError`, bypassing `AppError` handling and surfacing as an Express 500. This contradicts the desired behavior of returning structured 400 errors for bad inputs.

## Evidence
- src/controllers/imf.controller.ts:22-34
  ```ts
  const { countries, fields, start_year, end_year, isNea } = req.body as BaseRequest['body'];
  …
  if (fields.length === 0) {
    throw new AppError('Provide fields', 400);
  }
  ```
- No prior check ensures `fields` is defined or is an array.

## Impact
Clients who accidentally send `{ fields: null }` or omit the property receive a server error, polluting logs and hiding actionable feedback. Because the controller throws before hitting `AppError`, the response becomes the Express default HTML 500. The bug also undermines the upcoming schema validation effort.

## Proposed Change
Add an explicit guard that verifies `fields` is a non-empty array of strings before accessing `.length`. Integrate this with the broader schema validation helper (see dependency) so the controller only runs on validated input.

## Acceptance Criteria
- [ ] Requests missing `fields` or providing a non-array receive a 400 JSON error, not a 500.
- [ ] Automated tests cover missing `fields`, empty arrays, and valid arrays.
- [ ] Controller relies on validated input (no direct unsafe property access).

## Tasks
- [ ] Update controller to validate `fields` (pending shared schema).
- [ ] Add regression tests for the edge cases.
- [ ] Document the requirement in API docs.

## Rollout & Risk
Minimal risk; affects only error handling. Deploy with tests to confirm no behavior changes for valid payloads.

## References
- src/controllers/imf.controller.ts
- docs/backend/controllers/imf.md
