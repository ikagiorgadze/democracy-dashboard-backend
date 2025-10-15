# Module: src/controllers/imf.controller.ts

- **Purpose**: Handles IMF domain HTTP requests, providing health checks and normalized query responses.
- **Key types/interfaces**: Uses `BaseRequest` for shared payload shape and relies on `AppError` plus `formatError` for uniform error output.
- **Key functions/classes**: `getHealth` returns service status; `queryImfData` validates request body, enforces limits, and calls the IMF service.
- **Side effects & state**: No persistent state; throws `AppError` for validation failures and forwards service results to clients.
- **Dependencies**: Depends on `services/imf.service` for data retrieval and `utils/error` utilities for error handling.
- **How to extend**: Add schema validation (e.g., Joi/Zod) to centralize request checks or augment responses with metadata; ensure new handlers propagate `AppError` for consistent formatting.
