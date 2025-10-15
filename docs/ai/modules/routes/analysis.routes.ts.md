# Module: src/routes/analysis.routes.ts

- **Purpose**: Defines Express routing for analysis-related endpoints under `/analysis`.
- **Key types/interfaces**: Uses Express `Router`; no custom types.
- **Key functions/classes**: Exports a configured router wiring POST `/relationships/explain` and GET `/relationships/datasets/correlations` to controller handlers.
- **Side effects & state**: Registers routes when imported; no additional state.
- **Dependencies**: Depends on `controllers/analysis.controller` exports.
- **How to extend**: Add new analysis paths here, grouping them logically (e.g., `/relationships/...`); ensure corresponding controllers are implemented and imported.
