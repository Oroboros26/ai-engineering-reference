# Context Engineering

How to manage what goes into the context window — and how to scale beyond it.

---

## Context Window Fundamentals

```
[System Prompt] + [Conversation History] + [Retrieved Docs] + [Tools Schema]
= Total Input Tokens

Must fit within: context window limit (e.g. 200K for Claude)
```

**The problem:** Real-world data (codebases, documents, databases) vastly exceeds any context window. Context engineering is how you decide what fits.

---

## Strategies at a Glance

| Strategy | What It Is | Best For |
|---|---|---|
| **Full context** | Dump everything into one call | Short docs, ≤ 50K tokens |
| **Chunking** | Split doc into segments, process each | Batch summarization, extraction |
| **Sliding window** | Move window forward, keeping overlap | Sequential text, transcripts |
| **RAG** | Retrieve only relevant chunks | Q&A over large corpora |
| **Summarization** | Compress prior history | Long multi-turn conversations |
| **Hierarchical** | Summarize → detail on demand | Deep document analysis |

---

## RAG — Retrieve, Augment, Generate

```
User query
    ↓
[Retriever] — search vector store or keyword index
    ↓
Top-K relevant chunks (e.g. top 5 passages)
    ↓
[Augment] — inject chunks into prompt context
    ↓
[LLM] — generate answer grounded in retrieved context
    ↓
Response
```

### RAG vs Alternatives

| Approach | When to Use | Tradeoff |
|---|---|---|
| RAG | Large static knowledge base | Setup cost, retrieval quality |
| Long context (full doc) | ≤ 200K tokens, need full picture | Token cost |
| Fine-tuning | Fixed knowledge, high-volume | Expensive, retraining needed |
| Tool/API call | Live data (prices, weather) | Latency, reliability |

---

## Chunking Strategies

| Method | How | Use Case |
|---|---|---|
| **Fixed-size** | Split every N tokens with overlap | Simple, fast |
| **Sentence-based** | Split at sentence boundaries | Better coherence |
| **Semantic** | Split at topic/section boundaries | Best quality |
| **Recursive** | Try paragraph → sentence → word | Balanced default |

**Overlap rule:** Keep 10–20% overlap between chunks to avoid cutting context at a critical point.

---

## Retrieval Methods

| Method | How It Works | Best For |
|---|---|---|
| **BM25 (keyword)** | TF-IDF scoring on exact terms | Exact keyword matches |
| **Vector similarity** | Cosine distance of embeddings | Semantic / meaning-based |
| **Hybrid** | BM25 + vector, re-ranked | Best overall quality |
| **Reranking** | Cross-encoder re-scores top-K | Precision-critical systems |

**Practical default:** Use hybrid search (BM25 + vector) for most production RAG systems.

---

## Context Compression Techniques

```
1. Summarize conversation history after N turns
   "Summarize the discussion so far in 200 words."

2. Extract key facts only
   "From this document, extract only the facts relevant to: {query}"

3. Map-Reduce
   - Map: summarize each chunk independently
   - Reduce: combine summaries into final answer

4. Selective context
   Filter conversation history by relevance to current query
```

---

## Conversation History Management

```
Short conversations (< 10 turns):  keep full history
Long conversations (> 10 turns):   apply one of:
  - Rolling summary  (compress old turns)
  - Sliding window   (keep last N turns only)
  - Selective keep   (retain only "important" turns)
```

---

## Context Ordering Matters

Models perform better when:
- Instructions come **first** (system prompt)
- The most relevant content is at the **beginning** or **end** (not buried in the middle)
- Examples appear **before** the actual task

This is called the **Lost in the Middle** effect — models attend less to content in the center of a long context.

**See also:** [02-tokens.md](02-tokens.md) for token costs, [07-ai-elements.md](07-ai-elements.md) for embeddings used in RAG.
