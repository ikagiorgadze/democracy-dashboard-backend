# Module: src/utils/error.ts

- **Purpose**: Centralizes error representation with an `AppError` class and normalizes thrown errors into HTTP-friendly payloads.
- **Key types/interfaces**: `AppError` class with `status` and optional `details` fields.
- **Key functions/classes**: `formatError` converts unknown errors into `{ error, status, details? }` objects for JSON responses.
- **Side effects & state**: None; class instances hold per-error state only.
- **Dependencies**: No external dependencies; uses standard `Error` base class.
- **How to extend**: Enhance `formatError` to log or include trace IDs; create subclasses of `AppError` for specific domains when richer metadata is needed.
