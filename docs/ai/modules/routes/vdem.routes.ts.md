# Module: src/routes/vdem.routes.ts

- **Purpose**: Provides V-Dem endpoints under the `/v-dem` namespace.
- **Key types/interfaces**: Express `Router` only.
- **Key functions/classes**: Exports a router binding `/health` and `/query` routes to V-Dem controllers.
- **Side effects & state**: None; routes are registered on import.
- **Dependencies**: Uses handlers from `controllers/vdem.controller`.
- **How to extend**: Introduce additional V-Dem operations here (e.g., `/fields`); match controller signatures and update `app.ts` if new routers are created.
