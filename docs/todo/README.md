Project audit: 2025-10-15 16:37:00 UTC

Severity counts
- BLOCKER: 2
- HIGH: 5
- MEDIUM: 7
- LOW: 1

Type counts
- security: 3
- architecture: 2
- bug: 2
- reliability: 3
- testing: 5

| id | title | severity | type | component | status | estimate |
| --- | --- | --- | --- | --- | --- | --- |
| vdem-sql-injection | Parameterize V-Dem parquet queries | BLOCKER | security | src/services/vdem.service.ts | open | M |
| correlations-path-traversal | Sanitize correlation country paths | BLOCKER | security | src/services/correlations.service.ts | open | M |
| express-hardening | Add security headers and strict CORS | HIGH | security | src/app.ts | open | S |
| request-schema-validation | Introduce schema validation for requests | HIGH | architecture | src/controllers | open | M |
| analysis-payload-guard | Validate analysis explain payload | HIGH | bug | src/controllers/analysis.controller.ts | open | M |
| imf-fields-null-guard | Fix IMF request field validation | HIGH | bug | src/controllers/imf.controller.ts | open | S |
| metadata-definition-guard | Guard IMF metadata definition loads | HIGH | reliability | src/services/metadata-universal.service.ts | open | S |
| express-error-middleware | Install centralized error middleware | MEDIUM | architecture | src/app.ts | open | S |
| env-validation | Validate required environment config | MEDIUM | reliability | src/index.ts | open | M |
| graceful-shutdown | Implement graceful shutdown | MEDIUM | reliability | src/index.ts | open | S |
| testing-foundation | Establish automated test suite | MEDIUM | testing | repo | open | L |
| tests-vdem-service | Add tests for V-Dem query service | MEDIUM | testing | src/services/vdem.service.ts | open | M |
| tests-imf-service | Add tests for IMF service flows | MEDIUM | testing | src/services/imf.service.ts | open | L |
| tests-correlations-service | Add tests for correlation parquet access | MEDIUM | testing | src/services/correlations.service.ts | open | M |
| tests-ttl-cache | Add unit tests for TTL cache utility | LOW | testing | src/utils/ttlCache.ts | open | S |

Getting started
- Address BLOCKER security issues immediately (V-Dem SQL injection, correlation path traversal).
- Harden HTTP layer and request validation next to stabilize API behavior.
- Build out the test suite (framework + service coverage) so future fixes stay verified.
- Follow with reliability/configuration improvements and targeted utility tests.
