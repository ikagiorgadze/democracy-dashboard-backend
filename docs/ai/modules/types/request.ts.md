# Module: src/types/request.ts

- **Purpose**: Supplies shared request typings for controllers handling dataset queries.
- **Key types/interfaces**: `BaseQueryPayload` (countries, year bounds, fields, `isNea`) and `BaseRequest` extending Express `Request` with that body shape.
- **Key functions/classes**: None; interface declarations only.
- **Side effects & state**: None.
- **Dependencies**: Depends on Express `Request` type.
- **How to extend**: Add fields to `BaseQueryPayload` if new query parameters are introduced; ensure controllers validate any new optional properties.
