---
id: analysis-payload-guard
title: "Validate analysis explain payload"
severity: HIGH
type: bug
component: "src/controllers/analysis.controller.ts"
status: open
estimate: "M"
dependencies: ["request-schema-validation"]
---

## Rationale
`explainRelationshipsController` assumes `req.body` contains `indexA`, `indexB`, and `country` objects with nested properties. When the client omits any of these (or sends `indexA: null`), the controller throws before reaching its try/catch block, causing Express to emit an unstructured 500 error. The docs position controllers as the validation layer, but the implementation trusts unsanitized payloads.

## Evidence
- src/controllers/analysis.controller.ts:23-29
  ```ts
  const { indexA, indexB, country, execute } = req.body as ExplainRequest;
  const [metaA, metaB] = await Promise.all([
    getIndexMetaUniversal(indexA.name),
    getIndexMetaUniversal(indexB.name),
  ]);
  ```
- No guards before accessing `indexA.name` or `indexB.name`.

## Impact
Malformed requests crash the endpoint, returning default Express HTML and hiding meaningful feedback from API clients. Because this controller also triggers costly OpenAI calls, missing validation risks wasted tokens if future checks run late in the flow. The bug contradicts the architecture goal of thin, defensive controllers.

## Proposed Change
Add schema validation that requires `indexA`/`indexB` objects with `name` strings, optional `data` arrays, and a non-empty `country`. Reject invalid payloads with structured 400 responses before calling services. Ensure `execute` defaults safely.

## Acceptance Criteria
- [ ] Requests lacking required fields receive consistent 400 JSON errors.
- [ ] Validation prevents the controller from calling services with undefined inputs.
- [ ] Tests cover missing `indexA`, missing `country`, and valid payloads.
- [ ] Docs/OpenAPI updated to reflect the enforced payload shape.

## Tasks
- [ ] Implement schema validation (dependent on shared validation helper).
- [ ] Add regression tests.
- [ ] Update API reference docs.

## Rollout & Risk
Low risk—only error handling changes. Monitor logs post-deploy for new 400s indicating client fixes are needed.

## References
- src/controllers/analysis.controller.ts
- docs/backend/controllers/analysis.md
