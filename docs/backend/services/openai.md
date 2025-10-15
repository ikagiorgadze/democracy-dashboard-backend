# OpenAI Client Service

**Source**: `src/services/openai.service.ts`

## Responsibility
- Provide a singleton OpenAI client instance for narrative explanations without re-initializing the SDK on every request.

## Usage
- `getOpenAIClient()` lazily instantiates `new OpenAI({ apiKey })` and caches it in a module-level variable.
- Throws an error if `OPENAI_API_KEY` is missing; callers should catch and translate to an HTTP error.
- The calling controller (`analysis.controller.ts`) selects the model via `process.env.OPENAI_MODEL` (default `gpt-4.1`).

## Operational Notes
- Supply the API key via environment variable in development and production.
- Consider adding request logging, retrying, or rate limiting at the call site if the integration expands.
- For dry runs, omit `execute` in the explain endpoint; the prompt will be returned without touching the API.
