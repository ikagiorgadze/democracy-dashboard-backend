# Module: src/services/helpers/prompt.ts

- **Purpose**: Generates structured prompts for OpenAI explanations tying together metadata, correlation stats, and optional data series.
- **Key types/interfaces**: Uses `Correlation`, `IndexData`, and `IndexMeta` from `types/explain`.
- **Key functions/classes**: `buildExplainPrompt` assembles human-facing context, internal references, and strict output instructions tailored to the explanation workflow.
- **Side effects & state**: Pure function with no stored state.
- **Dependencies**: Imports type definitions only; no runtime dependencies beyond JSON serialization.
- **How to extend**: Adjust prompt format to align with new product requirements (e.g., different headings) or include additional contextual sections such as methodology disclaimers; keep instructions synchronized with consumer expectations in controllers.
