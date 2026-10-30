# Usage Optimization

How to reduce cost, improve speed, and get more value from every API call.

---

## The Optimization Levers

```
Cost  =  tokens used  ×  price per token
Speed =  f(model size, output length, cache hits)

To optimize: reduce tokens, choose cheaper model, maximize cache hits
```

---

## 1. Prompt Caching

Cache a fixed prefix of your prompt — if the same prefix is sent again within the TTL, you pay ~10% of the normal input price.

### How It Works

```
Call 1:  [System Prompt 5K tokens] + [User message]  →  Cache MISS, full price
Call 2:  [System Prompt 5K tokens] + [User message]  →  Cache HIT, 10% price
```

TTL: **5 minutes** (Anthropic). Cache is per-model, per-API-key.

### Claude Cache Breakpoint Syntax

```python
messages=[
    {
        "role": "user",
        "content": [
            {
                "type": "text",
                "text": "Your very long system context here...",
                "cache_control": {"type": "ephemeral"}  # ← cache breakpoint
            },
            {
                "type": "text",
                "text": "Actual user query"
            }
        ]
    }
]
```

### When Caching Pays Off

| Condition | Use Cache? |
|---|---|
| Same large system prompt per user session | Yes — big win |
| RAG: same document, many queries | Yes |
| One-off calls with unique context | No benefit |
| Prompt < 1024 tokens | No (min cache size) |

---

## 2. Batch API

Submit large numbers of requests asynchronously — typically 50% cheaper, but results take up to 24h.

### When to Use

```
Good for:            Bad for:
✓ Evals             ✗ Real-time user interactions
✓ Data extraction   ✗ Streaming responses
✓ Bulk annotation   ✗ Agentic loops
✓ Overnight jobs    ✗ Sub-second latency needs
```

### Anthropic Batch Example

```python
batch = client.messages.batches.create(
    requests=[
        {"custom_id": "row-1", "params": {"model": "claude-sonnet-4-6", "max_tokens": 100, "messages": [...]}},
        {"custom_id": "row-2", "params": {"model": "claude-sonnet-4-6", "max_tokens": 100, "messages": [...]}},
    ]
)
# Poll later: client.messages.batches.retrieve(batch.id)
```

---

## 3. Model Selection Framework

Pick the right model for the task — don't use Opus when Haiku will do.

| Task Type | Recommended Model | Reason |
|---|---|---|
| Simple classification, tagging | Haiku / GPT-4o mini | Fast, cheap, sufficient |
| Summarization, extraction | Sonnet / GPT-4o | Good quality, moderate cost |
| Complex reasoning, code review | Sonnet / GPT-4o | Usually sufficient |
| Hard math, multi-step logic | Opus / o3 | Quality matters more than cost |
| High-volume pipeline | Haiku / Gemini Flash | Volume drives costs up fast |
| Long doc (>100K tokens) | Gemini 2.5 Pro | Only 1M-context option |

**Rule:** Start with the middle tier, downgrade until quality degrades.

---

## 4. Token Reduction Tactics

| Tactic | How | Savings |
|---|---|---|
| Concise system prompts | Cut filler words, be direct | 20–40% on system prompt |
| Structured output | Request JSON instead of prose | 30–50% on output |
| Remove history | Summarize old turns instead of keeping raw | 50–80% on history tokens |
| Few-shot pruning | Use 2 examples instead of 6 | Per-call savings |
| Avoid repetition | Don't re-state instructions in every user turn | Cumulative savings |
| Specify output length | "In 2 sentences" / "max 100 words" | Constrains output tokens |

---

## 5. Streaming

Use streaming for user-facing applications to improve perceived latency — first token arrives faster, even if total time is similar.

```python
with client.messages.stream(
    model="claude-sonnet-4-6",
    max_tokens=500,
    messages=[{"role": "user", "content": "Explain RAG"}]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

---

## 6. Cost Estimation Cheat Sheet

```
Monthly cost estimate for a feature:

calls_per_day  = 1,000
avg_input_toks = 2,000
avg_output_toks = 300
model_input_rate = $3 / 1M (sonnet)
model_output_rate = $15 / 1M (sonnet)
days = 30

input_cost  = 1000 × 2000 / 1M × $3 × 30 = $180/mo
output_cost = 1000 × 300  / 1M × $15 × 30 = $135/mo
total ≈ $315/mo

With caching (80% hit rate on input):
input_cost ≈ 180 × (0.2 + 0.8×0.1) = $54/mo  ← 70% savings
```

**See also:** [02-tokens.md](02-tokens.md) for pricing tables, [01-models-reference.md](01-models-reference.md) for model specs.
