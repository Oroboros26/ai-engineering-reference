# AI Elements

Reference for the building blocks of AI systems: tools, agents, memory, embeddings, and evals.

---

## Tool Use / Function Calling

Tools let the model call external functions — databases, APIs, calculators, search engines.

### How It Works

```
1. You define tools (name, description, input schema)
2. Model decides to call a tool → returns tool_use block
3. You execute the function, return results
4. Model incorporates results → generates final response
```

### Tool Definition (Anthropic SDK)

```python
tools = [
    {
        "name": "get_weather",
        "description": "Get current weather for a city.",
        "input_schema": {
            "type": "object",
            "properties": {
                "city": {"type": "string", "description": "City name"},
                "units": {"type": "string", "enum": ["celsius", "fahrenheit"]}
            },
            "required": ["city"]
        }
    }
]

response = client.messages.create(
    model="claude-sonnet-4-6",
    tools=tools,
    messages=[{"role": "user", "content": "What's the weather in Paris?"}]
)
```

### Tool Use Decision Tree

```
Does the task require real-time or external data?  → Yes → Use tools
Does it require computation the model can't do?    → Yes → Use tools (calculator, code exec)
Is it pure reasoning / generation?                 → No tools needed
```

---

## Agents

An agent is an AI system that iterates — observing, reasoning, acting, and repeating until a goal is reached.

### ReAct Loop (Reason + Act)

```
Observe → Think → Act → Observe → Think → Act → ... → Done
   ↑                                                      |
   └──────────────── feedback loop ─────────────────────┘
```

### Agent Patterns

| Pattern | Description | Use Case |
|---|---|---|
| **Single agent** | One model, looped with tools | Most tasks |
| **Multi-agent** | Orchestrator delegates to specialists | Complex workflows |
| **Supervisor** | Manager agent routes to sub-agents | Large pipelines |
| **Debate / Self-review** | Model critiques its own output | High-stakes quality |
| **Parallel agents** | Multiple agents run in parallel, results merged | Speed, independent subtasks |

### Multi-Agent Example

```
Orchestrator Agent
    ├── Research Agent   (web search, document retrieval)
    ├── Analyst Agent    (data analysis, code execution)
    └── Writer Agent     (drafting, formatting)
```

### When to Use Agents vs Single Call

| Use Single Call | Use Agent |
|---|---|
| Task fits in one prompt | Task has multiple distinct steps |
| No external data needed | Requires tool use in a loop |
| Deterministic output | Outcome depends on intermediate results |
| Latency is critical | Quality > speed |

---

## Memory Types

| Type | Where Stored | Scope | Example |
|---|---|---|---|
| **In-context** | Model's context window | Current session only | Conversation history |
| **External (vector)** | Vector database | Persistent, semantic search | RAG knowledge base |
| **External (key-value)** | Database / file | Persistent, exact lookup | User preferences |
| **Procedural** | System prompt / CLAUDE.md | Persistent instructions | Coding style rules |

**In practice:** most production agents use a combination — in-context for the current turn, external DB for long-term memory.

---

## Embeddings

An embedding is a numerical vector representation of text — similar meaning → similar vectors.

### Key Properties

```
"dog"    →  [0.23, -0.11, 0.87, ...]   ← 1536-dimensional vector
"puppy"  →  [0.25, -0.09, 0.85, ...]   ← similar vector (close in space)
"car"    →  [0.71, 0.42, -0.33, ...]   ← distant vector
```

### Common Use Cases

| Use Case | How |
|---|---|
| Semantic search | Embed query + docs, find nearest neighbors |
| RAG retrieval | Embed chunks, store in vector DB, retrieve by similarity |
| Clustering | Group similar documents |
| Classification | Train a classifier on top of embeddings |
| Deduplication | Find near-duplicate content |

### Embedding Models

| Model | Dimensions | Use Case |
|---|---|---|
| `text-embedding-3-large` (OpenAI) | 3072 | High quality |
| `text-embedding-3-small` (OpenAI) | 1536 | Cost-efficient |
| `embed-english-v3.0` (Cohere) | 1024 | Strong retrieval |
| `all-MiniLM-L6-v2` (HuggingFace) | 384 | Local / free |

### Vector Databases

| DB | Type | Notes |
|---|---|---|
| Pinecone | Managed | Fully managed, easy setup |
| Weaviate | Open-source / managed | Hybrid search built-in |
| Qdrant | Open-source / managed | Fast, Rust-based |
| pgvector | PostgreSQL extension | Good for existing Postgres setups |
| Chroma | Open-source | Simple, good for prototyping |

---

## Evals

Evals measure whether your AI system is actually working.

### Why Evals Matter

Without evals, you're guessing. Changes to prompts, models, or data can silently degrade quality.

### Types of Evals

| Type | How | Best For |
|---|---|---|
| **Deterministic** | Exact match, regex, JSON schema validation | Structured outputs |
| **LLM-as-judge** | Another model grades the output | Open-ended quality |
| **Human eval** | Human raters score outputs | Ground truth, high stakes |
| **Retrieval eval** | Precision/recall on retrieved chunks | RAG systems |
| **End-to-end** | Full pipeline output vs expected | Integration quality |

### LLM-as-Judge Pattern

```python
judge_prompt = """
Rate the following answer on a scale of 1–5 for accuracy and helpfulness.

Question: {question}
Answer: {answer}

Return JSON: {"accuracy": <1-5>, "helpfulness": <1-5>, "reasoning": "<str>"}
"""
```

### Eval Metrics Quick Reference

| Metric | Formula | Use For |
|---|---|---|
| Accuracy | correct / total | Classification |
| Precision | TP / (TP + FP) | Retrieval, extraction |
| Recall | TP / (TP + FN) | Retrieval coverage |
| ROUGE | Overlap of n-grams | Summarization |
| Latency p50/p95 | Percentile response time | Production monitoring |
| Cost per correct answer | cost / correct_outputs | ROI |

**See also:** [03-prompt-engineering.md](03-prompt-engineering.md) for prompt patterns, [04-context-engineering.md](04-context-engineering.md) for RAG, [06-usage-optimization.md](06-usage-optimization.md) for cost control.
