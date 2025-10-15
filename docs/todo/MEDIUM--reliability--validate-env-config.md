---
id: env-validation
title: "Validate required environment config"
severity: MEDIUM
type: reliability
component: "src/index.ts"
status: open
estimate: "M"
dependencies: []
---

## Rationale
The bootstrap sequence only calls `dotenv.config()` and immediately starts listening. There is no guard that required environment variables (dataset paths, OpenAI key, timeouts) are set correctly for the target deployment. Documentation stresses that environment variables must be prepared before boot, yet the service will happily start with missing or malformed values and fail later (e.g., first OpenAI call throws “OPENAI_API_KEY is required”). Early, explicit validation reduces mystery failures.

## Evidence
- src/index.ts:1-6 – loads `.env`, resolves `PORT`, and calls `app.listen` without any validation or logging.
- docs/backend/operations.md – instructs operators to set numerous env vars but no code enforces them.

## Impact
Misconfigured environments lead to runtime crashes deep in request handling (e.g., IMF timeout parsing, missing parquet paths), making outages harder to diagnose. Booting without validation also hides typos in env var names across environments.

## Proposed Change
Introduce an environment schema (zod or similar) that runs during startup, validating required variables, types, defaults, and mutually exclusive options. Fail fast with a clear error if validation fails.

## Acceptance Criteria
- [ ] App validates environment configuration before starting the HTTP listener.
- [ ] Startup fails with actionable error messages when required variables are missing or invalid.
- [ ] Defaults are centralized in one config module instead of spread throughout services.
- [ ] Documentation references the validation and lists required variables.

## Tasks
- [ ] Create `src/config/env.ts` (or similar) defining schema and defaults.
- [ ] Update services to consume config values from the validated module.
- [ ] Add unit tests for config parsing and failure scenarios.
- [ ] Document the new behavior in docs/backend/operations.md and README.

## Rollout & Risk
Deploy to staging with the validator enabled to catch missing secrets safely. Because the change alters startup behavior, ensure infrastructure is ready to provide all required variables before production rollout.

## References
- src/index.ts
- docs/README.md
- docs/backend/operations.md
