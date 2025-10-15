# Type Definitions

## Request Payloads
- **`BaseQueryPayload`** (`src/types/request.ts`): shared shape for dataset queries with `countries`, optional `start_year`, `end_year`, `fields`, and IMF-specific `isNea`.
- **`BaseRequest`**: Express `Request` extension enforcing the `BaseQueryPayload` body.

## Explanation Domain (`src/types/explain.ts`)
- **`IndexMeta`**: `{ index_code, name, question, definition }` metadata used in prompts and responses.
- **`IndexData`**: single observation `{ year, observation }`. Arrays appear inside `Index`.
- **`Index`**: `{ name, data }` pair describing an indicator supplied to the explanation endpoint.
- **`ExplainRequest`**: body structure for `/analysis/relationships/explain`, containing `indexA`, `indexB`, `country`, and optional `execute`.
- **`Correlation`**: numeric summary with `r`, optional `n`, `method`, `p_value`, and year bounds.
- **`CorrelationPair`**: export shape for ranked results with `indexA`, `indexB`, `r`, `n`, `p_value`.
- **`CorrelationsRequest`**: query parameters accepted by `/analysis/relationships/datasets/correlations`.
- **`CorrelationsResponse`**: service response containing an array of `CorrelationPair`.
- **`ExplainResponse`**: structure returned by the explanation controller when not executing (includes prompt, metadata context, and optional cached explanation).
