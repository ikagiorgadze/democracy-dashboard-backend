# Module: src/app.ts

- **Purpose**: Constructs and configures the Express application, wiring global middleware and domain routers.
- **Key types/interfaces**: Relies on Express `Application` and CORS `CorsOptions`; no custom types defined.
- **Key functions/classes**: Default export is the configured Express app instance.
- **Side effects & state**: Evaluates CORS origin settings from `process.env.CORS_ORIGIN`, registers middleware stack, and defines a `/health` GET route.
- **Dependencies**: Pulls routers from `routes/vdem.routes`, `routes/imf.routes`, and `routes/analysis.routes`; uses `cors` and `express` packages.
- **How to extend**: Add security middleware (e.g., Helmet) or request logging before router mounting; register additional routers under `/api`-style prefixes to namespace features.
