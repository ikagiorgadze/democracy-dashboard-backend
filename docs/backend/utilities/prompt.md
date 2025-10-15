# Explain Prompt Builder

**Source**: `src/services/helpers/prompt.ts`

## Responsibility
- Assemble a structured prompt for the explanation endpoint, ensuring consistent tone, headers, and instructions before sending to OpenAI.

## Input
- `buildExplainPrompt({ metaA, metaB, country, correlation, data? })`
  - `metaA` / `metaB`: indicator metadata (`IndexMeta`) with human-friendly names.
  - `country`: target country name to reference in the narrative.
  - `correlation`: correlation statistics (`r`, `n`, `p_value`, optional years).
  - `data`: optional `IndexData` samples for concrete examples.

## Output
- Returns a multiline string with:
  - A headline instructing the model to explain the relationship using friendly indicator names.
  - Context lines summarizing correlation strength and years covered.
  - A formatted metadata block omitting raw indicator codes.
  - Detailed instructions enforcing six mandatory headers: `Summary`, `Why it matters`, `Statistical reliability`, `Drivers/Context`, `Real examples`, `Caveats`.
  - An internal reference footer listing raw indicator codes with explicit guidance not to mention them.

## Usage Notes
- Designed to prevent the model from exposing technical codes in the response while still providing statistical context.
- When `data` is supplied, the prompt serializes it as JSON for the model to reference. Omit large payloads to keep prompts concise.
- Controllers combine this prompt with cached correlation data; only include real observations that align with the indicator metadata.
