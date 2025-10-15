# Module: src/controllers/analysis.controller.ts

- **Purpose**: Exposes analysis endpoints for generating correlation explanations and retrieving ranked correlations between datasets.
- **Key types/interfaces**: Consumes `ExplainRequest`, `CorrelationsRequest`, and `CorrelationsResponse` from `types/explain`.
- **Key functions/classes**: `explainRelationshipsController` orchestrates metadata lookups, correlation queries, prompt creation, optional OpenAI execution, and caching; `getCorrelationsController` validates query params and delegates to the correlations service.
- **Side effects & state**: Maintains an in-memory `TTLCache` for explanation responses; interacts with external OpenAI API when `execute` flag is true.
- **Dependencies**: Services `metadata-universal`, `correlations`, `openai`, prompt helper, TTL cache utility, and Express `Request`/`Response` types.
- **How to extend**: Introduce centralized validation (e.g., zod) before service calls, or expand caching strategy; when adding new analysis endpoints, ensure consistent error handling (HTTP 400/500) and reuse service abstractions.
