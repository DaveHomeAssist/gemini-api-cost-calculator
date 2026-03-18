# LLM API Cost Calculator

Static cost-comparison calculator for Gemini, GPT-4, Claude, Llama, Mistral, DeepSeek, and Qwen pricing scenarios.

## Run

Open [`docs/index.html`](./docs/index.html) in a browser.

## Pinned Example Scenario

Use this first if you want the page to make sense immediately:

- Input tokens: `25,000`
- Output tokens: `8,000`
- Requests per day: `200`
- Batch API discount: `off`
- Context caching: `off`

This gives you a simple “medium-sized production workflow” comparison: a prompt-heavy assistant call with non-trivial output volume, repeated enough times per day that provider price differences become obvious.
