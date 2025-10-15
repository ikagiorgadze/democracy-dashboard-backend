# Module: src/types/explain.ts

- **Purpose**: Defines shared TypeScript types for metadata, explanation requests/responses, and correlation payloads.
- **Key types/interfaces**: `IndexMeta`, `IndexData`, `Index`, `ExplainRequest`, `Correlation`, `CorrelationPair`, `CorrelationsRequest`, `CorrelationsResponse`, `ExplainResponse`.
- **Key functions/classes**: None; type declarations only.
- **Side effects & state**: None.
- **Dependencies**: Pure TypeScript types with no imports beyond built-ins.
- **How to extend**: Add new DTOs as analysis features expand; ensure controllers/services update their typings and maintain compatibility with API responses.
