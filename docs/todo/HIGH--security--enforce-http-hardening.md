---
id: express-hardening
title: "Add security headers and strict CORS"
severity: HIGH
type: security
component: "src/app.ts"
status: open
estimate: "S"
dependencies: []
---

## Rationale
The Express app exposes public APIs but ships without baseline HTTP hardening. `app.ts` even carries TODO comments about adding Helmet and revisiting CORS, yet the current implementation leaves CORS completely open when `CORS_ORIGIN` is unset and omits security headers (XSS, clickjacking, HSTS). The documentation highlights that middleware should be configured in `src/app.ts`, so leaving the TODOs unresolved is a concrete gap.

## Evidence
- src/app.ts:9-32
  ```ts
  // TODO - Add Helmet for security headers
  // TODO - CORS configuration (allow from env-specified origins or all in dev)
  …
  const corsOrigin: CorsOptions['origin'] = corsOriginEnv
    ? corsOriginEnv.split(',').map((s) => s.trim())
    : true; // allow all if not specified (useful in local dev)
  ```
- No middleware sets `Referrer-Policy`, `X-Content-Type-Options`, or other security headers.

## Impact
Unauthenticated origins can hit the API by default, which risks CSRF-style abuse from random sites embedding the endpoints. Missing protective headers leaves clients more exposed to XSS, MIME sniffing, and clickjacking when responses are rendered in browsers. These gaps contravene standard API security expectations and the TODOs indicate they were already flagged.

## Proposed Change
Install and configure Helmet with an API-friendly policy (disable CSP if needed but keep frameguard, hsts for non-local, etc.). Replace the permissive CORS default with an allowlist sourced from `CORS_ORIGIN`; fall back to localhost development URLs when unset rather than `*`. Document how to set multiple origins and how to opt into wide-open access for trusted environments only.

## Acceptance Criteria
- [ ] Helmet (or equivalent) middleware sets a baseline security header set.
- [ ] Default CORS configuration no longer allows every origin; development defaults are explicit and production requires env configuration.
- [ ] Automated or manual tests confirm expected headers and CORS responses.
- [ ] Docs explain the new defaults and how to configure additional origins.

## Tasks
- [ ] Add Helmet with configurable directives (differentiate dev vs prod).
- [ ] Replace `true` fallback with explicit localhost-only defaults, and error if no origins supplied in production.
- [ ] Extend operations docs with updated CORS/helmet guidance.
- [ ] Add integration test asserting security headers on `/health`.

## Rollout & Risk
Deploy to staging to verify that legitimate clients have the correct Origin headers. If a consumer breaks due to missing CORS config, update the allowlist env var. Helmet header changes are low risk but confirm no downstream proxies strip or conflict with them.

## References
- docs/backend/overview.md
- docs/backend/routes.md
- src/app.ts
