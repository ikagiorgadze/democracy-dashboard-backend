---
id: request-schema-validation
title: "Introduce schema validation for requests"
severity: HIGH
type: architecture
component: "src/controllers"
status: open
estimate: "M"
dependencies: []
---

## Rationale
Controllers currently rely on ad-hoc checks and direct property access, which conflicts with the documentation’s goal of “thin controllers” with consistent validation. The README even lists “Request validation relies on manual checks; a schema validator would improve error consistency.” As shipped, endpoints access nested fields without verifying required structure, leading to runtime crashes and inconsistent error responses across `/v-dem`, `/imf`, and `/analysis`.

## Evidence
- docs/README.md (Known Limitations): “Request validation relies on manual checks…”
- src/controllers/imf.controller.ts:17-36 – manual length checks (`fields.length`) without confirming `fields` is an array.
- src/controllers/analysis.controller.ts:23-30 – destructures `indexA`/`indexB` without guarding against missing properties.
- src/controllers/vdem.controller.ts:21-33 – only verifies body is an object; does not enforce types, field counts, or value ranges.

## Impact
Malformed payloads produce 500s instead of friendly 400s (e.g., missing `fields` for IMF crashes the controller). Lack of consistent validation opens the door to injection vectors (see V-Dem TODO) and makes it hard to evolve the API safely. Clients receive unpredictable error shapes, and service operators cannot rely on validation to protect downstream services.

## Proposed Change
Adopt a schema validation layer (zod is already a dependency) to define payload contracts per endpoint. Validate in controllers before passing to services; surface `AppError` with detailed messages on failure. Centralize the validation utilities to keep controllers thin.

## Acceptance Criteria
- [ ] Each public route validates the request body/query against a schema, rejecting invalid payloads with 4xx responses.
- [ ] Controllers no longer access optional fields without null/undefined checks.
- [ ] Negative tests cover representative malformed payloads for `/v-dem`, `/imf`, `/analysis`.
- [ ] Documentation reflects the enforced request shapes.

## Tasks
- [ ] Define zod schemas for V-Dem, IMF, and analysis endpoints.
- [ ] Implement reusable validation helpers to map schema errors to AppError.
- [ ] Add tests that assert 400 responses on invalid payloads.
- [ ] Update API docs / OpenAPI spec to match enforced contracts.

## Rollout & Risk
Start by instrumenting validation logging in a staging environment to observe rejected payloads. Communicate schema changes to frontend consumers before deployment. If unexpected payloads appear, adjust schemas while maintaining the principle of rejecting malformed requests.

## References
- docs/backend/controllers/*.md
- docs/backend/routes.md
- src/controllers/*.ts
