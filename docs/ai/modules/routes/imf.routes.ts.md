# Module: src/routes/imf.routes.ts

- **Purpose**: Exposes IMF dataset endpoints under the `/imf` prefix.
- **Key types/interfaces**: Uses Express `Router` only.
- **Key functions/classes**: Exports a router that binds `/health` and `/query` paths to IMF controllers.
- **Side effects & state**: None beyond route registration.
- **Dependencies**: Imports controller handlers from `controllers/imf.controller`.
- **How to extend**: Add more IMF-specific routes here, maintaining consistent naming (e.g., `/metadata`); remember to mount them in `app.ts` if new prefixes are introduced.
