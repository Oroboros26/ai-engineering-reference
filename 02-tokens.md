# Tokens

Everything you need to know about tokens — what they are, how they're counted, and what they cost.

---

## What Is a Token?

A token is the smallest unit of text a model reads or writes. Not a word, not a character — somewhere in between.

```
"Hello, world!"  →  ["Hello", ",", " world", "!"]  =  4 tokens
"tokenization"   →  ["token", "ization"]            =  2 tokens
```

**Rules of thumb:**
- ~4 characters per token (English)
- ~750 words ≈ 1,000 tokens
- 1 page of text ≈ ~500–750 tokens
- Code is denser — often 1 token per 2–3 characters

---

## Input vs Output Tokens

| Type | Definition | Cost |
|---|---|---|
| Input tokens | Everything sent TO the model: system prompt + conversation history + tools | Cheaper |
| Output tokens | Everything the model GENERATES back | 3–5x more expensive |
| Cache read tokens | Input tokens served from prompt cache | Cheapest (~10% of input cost) |

**Why output costs more:** Generating tokens is autoregressive (one at a time); reading them is parallelizable.

---

## Context Window = Input Budget + Output Budget

```
Context window = max_input_tokens + max_output_tokens

Example: 200K context window
  - You send 150K tokens of input
  - Model can generate up to 50K tokens of output
  - Total ≤ 200K
```

If input fills the context window, the model has no room to respond.

---

## Pricing Reference (per 1M tokens, approx. mid-2025)

| Model | Input ($/1M) | Output ($/1M) | Cache Read ($/1M) |
|---|---|---|---|
| claude-opus-4-7 | $15 | $75 | $1.50 |
| claude-sonnet-4-6 | $3 | $15 | $0.30 |
| claude-haiku-4-5 | $0.80 | $4 | $0.08 |
| GPT-4o | $2.50 | $10 | $1.25 |
| GPT-4o mini | $0.15 | $0.60 | $0.075 |
| Gemini 2.5 Pro | $1.25 | $10 | $0.31 |
| Gemini 2.5 Flash | $0.15 | $0.60 | $0.037 |

**Always verify current pricing at official provider docs — these change frequently.**

---

## How to Count Tokens

### Anthropic (Claude)
```python
import anthropic
client = anthropic.Anthropic()

response = client.messages.count_tokens(
    model="claude-sonnet-4-6",
    system="You are a helpful assistant.",
    messages=[{"role": "user", "content": "Hello"}]
)
print(response.input_tokens)
```

### OpenAI (tiktoken)
```python
import tiktoken
enc = tiktoken.encoding_for_model("gpt-4o")
tokens = enc.encode("Hello, world!")
print(len(tokens))  # 4
```

### Google
Use `model.count_tokens()` in the Gemini SDK.

---

## Cost Estimation Formula

```
Cost = (input_tokens / 1,000,000 × input_rate)
     + (output_tokens / 1,000,000 × output_rate)

Example:
  10,000 input tokens  + 500 output tokens on claude-sonnet-4-6
  = (10,000 / 1M × $3) + (500 / 1M × $15)
  = $0.03 + $0.0075
  = $0.0375 per call
```

---

## Token Reduction Quick Tips

- Be concise in system prompts — every token costs money on every call
- Use structured output (JSON) to reduce verbose prose responses
- Remove redundant context from conversation history
- Use prompt caching for repeated long prompts (see [06-usage-optimization.md](06-usage-optimization.md))
