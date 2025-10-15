---
id: analysis-correlations-validation
title: "Validate correlations query parameters"
severity: HIGH
type: bug
component: "src/controllers/analysis.controller.ts"
status: open
estimate: "S"
dependencies: ["request-schema-validation"]
---

## Rationale
`getCorrelationsController` trusts `req.query` and performs ad-hoc string checks. Query parameters arrive as strings (or arrays) but the controller compares them to enums with `includes` and calls `parseInt` without guarding against `NaN`. Invalid inputs therefore slip through or produce 500s later in `getTopCorrelations`. The API needs consistent validation and error handling for every endpoint; this handler currently violates that requirement.

## Evidence
- src/controllers/analysis.controller.ts:58-98  
  ```ts
  const query = req.query as unknown as CorrelationsRequest;
  …
  if (!country || !type || !dataset1 || !dataset2) {
    return res.status(400) …
  }
  …
  const minObs = minObservations ? parseInt(String(minObservations), 10) : undefined;
  const lim = 5; // Always return exactly 3 correlation pairs
  const correlations = await getTopCorrelations(country, type, dataset1, dataset2, minObs, lim);
  ```
- No checks ensure `minObservations` or `limit` are numbers; non-numeric input becomes `NaN` and propagates downstream.
- Casting `req.query` bypasses TypeScript safeguards and hides potential array inputs (Express can send `string[]`).

## Impact
Malformed requests either bypass validation and trigger unexpected behaviour (e.g., `minObservations=abc` becomes `NaN` and disables the filter) or surface as confusing errors from the service layer. This undermines API reliability and conflicts with the directive that all endpoints need proper validation and error handling.

## Proposed Change
Introduce explicit query validation (e.g., zod schema) to coerce and verify query parameters before calling the service. Reject invalid enums, non-numeric limits, and unexpected arrays with structured 400 responses. Align the hard-coded limit comment with actual behaviour or expose it as a validated parameter.

## Acceptance Criteria
- [ ] GET `/analysis/relationships/datasets/correlations` validates query parameters using shared schemas.
- [ ] Invalid enum values or non-numeric parameters return consistent 400 JSON errors.
- [ ] TypeScript no longer requires unsafe casts of `req.query`.
- [ ] Tests cover invalid combinations and ensure the service is only called with normalized inputs.

## Tasks
- [ ] Add schema validation (depends on shared helper) for correlations queries.
- [ ] Update controller to rely on parsed, typed parameters.
- [ ] Write regression tests covering invalid enums, duplicate query values, and numeric parsing.
- [ ] Document expected query parameters in API docs/OpenAPI.

## Rollout & Risk
Low risk; only error handling changes. Deploy alongside schema validation work and monitor for increased 400 responses indicating clients must adjust.

## References
- src/controllers/analysis.controller.ts
- docs/backend/controllers/analysis.md
