# Error Handling Utilities

**Source**: `src/utils/error.ts`

## `AppError`
- Extends `Error` with an HTTP `status` code and optional `details`.
- Ensures prototype correction so `instanceof AppError` works after transpilation.
- Use for predictable client-facing failures (validation errors, missing files, upstream API issues).

## `formatError(err)`
- Converts thrown values into a serializable `{ error, status, details? }` object.
- Preserves `status` and `details` when the error is an `AppError`.
- Defaults to HTTP 500 for unexpected errors or non-Error throwables.

## Controller Pattern
- Controllers wrap logic in try/catch and pass errors through `formatError`, keeping JSON responses consistent.
- Avoid throwing plain strings; wrap intentional responses in `AppError` to control status codes.
