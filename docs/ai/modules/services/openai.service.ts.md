# Module: src/services/openai.service.ts

- **Purpose**: Provides a lazily instantiated OpenAI API client for use across the application.
- **Key types/interfaces**: Relies on the `OpenAI` class from the official SDK.
- **Key functions/classes**: `getOpenAIClient` returns a singleton client, throwing if `OPENAI_API_KEY` is absent.
- **Side effects & state**: Stores the constructed client in module-level state; reads `process.env.OPENAI_API_KEY`.
- **Dependencies**: External `openai` package and environment configuration.
- **How to extend**: Wrap the client with request logging or instrumentation; support configurable organization or project IDs before returning the client.
