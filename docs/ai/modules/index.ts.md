# Module: src/index.ts

- **Purpose**: Bootstraps environment configuration and starts the Express application exported from `app.ts`.
- **Key types/interfaces**: None declared; uses `process.env` and Express application types transitively.
- **Key functions/classes**: Invokes `dotenv.config()` and `app.listen(PORT)`; no custom exports beyond the server side-effect.
- **Side effects & state**: Reads environment variables, starts an HTTP listener on `PORT` (default 3000).
- **Dependencies**: `dotenv` for env loading; local module `./app` for the Express instance.
- **How to extend**: If you need logging on startup, pass a callback to `app.listen`; for graceful shutdown integrate signal handlers here.
