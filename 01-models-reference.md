# Models Reference

Quick-reference for all major AI model families as of mid-2025.

---

## Claude (Anthropic)

| Model | Context Window | Strengths | Best For | Cost Tier |
|---|---|---|---|---|
| claude-opus-4-7 | 200K tokens | Highest reasoning, complex tasks | Hard problems, research, long docs | $$$$$ |
| claude-opus-4-6 | 200K tokens | Strong reasoning + tool use | Complex coding, analysis | $$$$ |
| claude-sonnet-4-6 | 200K tokens | Balance of speed + intelligence | Most production tasks | $$$ |
| claude-haiku-4-5 | 200K tokens | Fastest, cheapest | High-volume, simple tasks, classification | $ |

**Notes:**
- All Claude models support tool use, vision, and extended thinking (Opus/Sonnet)
- Output tokens cost ~3–5x more than input tokens across the family
- claude-sonnet-4-6 is the default recommended model for most workloads

---

## GPT (OpenAI)

| Model | Context Window | Strengths | Best For | Cost Tier |
|---|---|---|---|---|
| GPT-4o | 128K tokens | Multimodal, fast, strong reasoning | General production use | $$$ |
| GPT-4o mini | 128K tokens | Fast, cheap, good quality | High-volume simpler tasks | $ |
| GPT-4 Turbo | 128K tokens | Strong reasoning | Complex analysis | $$$$ |
| o3 | 200K tokens | Deep reasoning (thinking model) | Math, code, hard logic | $$$$$ |
| o4-mini | 128K tokens | Fast reasoning model | Reasoning at lower cost | $$ |

---

## Gemini (Google)

| Model | Context Window | Strengths | Best For | Cost Tier |
|---|---|---|---|---|
| Gemini 2.5 Pro | 1M tokens | Massive context, multimodal | Long documents, codebases | $$$$ |
| Gemini 2.5 Flash | 1M tokens | Fast, large context | High-volume + large context | $$ |
| Gemini 2.0 Flash | 1M tokens | Speed-optimized | Real-time applications | $ |

**Note:** Gemini's 1M context window is the largest available in production.

---

## Open-Source / Self-Hosted

| Model | Context Window | Strengths | Best For |
|---|---|---|---|
| Llama 3.3 70B | 128K tokens | Strong open-weight perf | Self-hosted production |
| Llama 3.1 405B | 128K tokens | Best open-weight quality | Research, fine-tuning base |
| Mistral Large 2 | 128K tokens | Strong multilingual | European/multilingual tasks |
| Qwen2.5 72B | 128K tokens | Strong coding + Chinese | Code generation |
| DeepSeek V3 | 64K tokens | Strong reasoning, cheap | Cost-sensitive reasoning |

---

## Model Selection Framework

```
Task type → Model tier recommendation

Simple classification / extraction   → Haiku, GPT-4o mini, Gemini Flash
Standard coding / Q&A                → Sonnet, GPT-4o
Complex reasoning / long docs        → Opus, GPT-4 Turbo, Gemini Pro
Math / hard logic / proofs           → o3, claude with extended thinking
Very long documents (>100K tokens)   → Gemini 2.5 Pro (1M context)
Cost-sensitive, high-volume          → Haiku, GPT-4o mini, open-source
```

---

## Context Window Quick Reference

| Provider | Max Context | Notes |
|---|---|---|
| Google Gemini | 1,000,000 tokens | Largest available |
| Anthropic Claude | 200,000 tokens | Consistent across family |
| OpenAI GPT | 128,000 tokens | Standard across GPT-4 |
| Meta Llama 3 | 128,000 tokens | Open-weight |

**See also:** [02-tokens.md](02-tokens.md) for token counting and cost math.
