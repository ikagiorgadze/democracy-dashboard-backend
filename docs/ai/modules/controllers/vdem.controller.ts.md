# Module: src/controllers/vdem.controller.ts

- **Purpose**: Serves V-Dem dataset routes, mapping HTTP requests to DuckDB-backed queries.
- **Key types/interfaces**: Uses `BaseRequest` for typed body access.
- **Key functions/classes**: `getHealth` reports service readiness; `queryVdemDataController` validates body presence, adapts BigInt results, and calls the V-Dem service.
- **Side effects & state**: No long-lived state; converts `bigint` fields to numbers to ensure JSON serialization compatibility.
- **Dependencies**: Relies on `services/vdem.service` for data access and `utils/error` for normalization of unexpected errors.
- **How to extend**: Add schema validation for body payload, provide pagination/filters, or enrich responses; keep BigInt handling in sync with service changes.
