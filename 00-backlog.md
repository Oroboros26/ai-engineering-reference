# Knowledge Bank — Backlog

Topics not yet covered. Each row is a candidate for a new reference card in the next session.

---

## Priority 1 — Core Gaps (Most Useful Next)

| Topic | What to Cover | Suggested File Name |
|---|---|---|
| **Extended Thinking / Reasoning Models** | Claude extended thinking, o3, DeepSeek R1 — when to use, budget_tokens, tradeoffs vs standard | `08-reasoning-models.md` |
| **Fine-Tuning** | When to fine-tune vs prompt, data requirements, LoRA, RLHF, OpenAI vs Anthropic fine-tune options | `09-fine-tuning.md` |
| **Safety & Guardrails** | Prompt injection, jailbreaks, output validation, content filtering, input sanitization | `10-safety-guardrails.md` |
| **Multimodal (Vision + Audio)** | Image inputs, PDF processing, OCR pipelines, Whisper for audio, video support per model | `11-multimodal.md` |
| **Observability & Monitoring** | Logging AI calls, tracing, cost dashboards, LangSmith, Helicone, error tracking in prod | `12-observability.md` |

---

## Priority 2 — Frameworks & Tooling

| Topic | What to Cover | Suggested File Name |
|---|---|---|
| **Orchestration Frameworks** | LangChain, LlamaIndex, DSPy, CrewAI, AutoGen — what each does, when to use, tradeoffs | `13-orchestration-frameworks.md` |
| **Prompt Management & Versioning** | Storing prompts as code, version control, A/B testing prompts, PromptFoo, LangSmith Hub | `14-prompt-management.md` |
| **Async / Parallel API Patterns** | Concurrent requests, rate limit handling, exponential backoff, queue-based pipelines | `15-async-patterns.md` |
| **Model Self-Hosting** | Ollama, vLLM, Together AI, Replicate — running open-source models locally or on cloud | `16-self-hosting.md` |
| **Structured Output Deep Dive** | JSON mode, tool-based extraction, Pydantic integration, schema validation, reliability tricks | `17-structured-output.md` |

---

## Priority 3 — Specialized Topics

| Topic | What to Cover | Suggested File Name |
|---|---|---|
| **AI Security** | Prompt injection attacks, data exfiltration, adversarial prompts, red-teaming, defenses | `18-ai-security.md` |
| **Document Processing Pipelines** | PDF extraction, table parsing, chunking strategies for structured docs, OCR + LLM patterns | `19-document-pipelines.md` |
| **Vector Database Deep Dive** | Pinecone, pgvector, Weaviate, Qdrant — indexing strategies, ANN algorithms, hybrid search setup | `20-vector-databases.md` |
| **Evals Deep Dive** | Full eval harness setup, PromptFoo, RAGAS for RAG eval, LLM-as-judge at scale, CI/CD evals | `21-evals-deep-dive.md` |
| **Cost Monitoring in Production** | Tracking spend per user/feature, alerts, dashboards, anomaly detection on API costs | `22-cost-monitoring.md` |

---

## Priority 4 — Platform-Specific (Relevant to This Project)

| Topic | What to Cover | Suggested File Name |
|---|---|---|
| **Snowflake Cortex AI** | Cortex functions (COMPLETE, SUMMARIZE, etc.), ML functions, vector search in Snowflake, integration with dbt | `23-snowflake-cortex.md` |
| **AI + dbt Integration** | Using LLMs to generate/test dbt models, AI-assisted data pipelines, semantic layer + AI | `24-dbt-ai-integration.md` |
| **Claude-Specific Features** | Computer use, citations API, files API, tool use advanced patterns, system prompt caching | `25-claude-advanced.md` |

---

## Topics Already Covered

For reference — these have a reference card in the bank:

- Models reference → `01-models-reference.md`
- Tokens (input/output, pricing, counting) → `02-tokens.md`
- Prompt engineering → `03-prompt-engineering.md`
- Context engineering + RAG → `04-context-engineering.md`
- Claude Code harness → `05-harness-engineering.md`
- Usage optimization (caching, batching, cost) → `06-usage-optimization.md`
- AI elements (tools, agents, memory, embeddings, evals overview) → `07-ai-elements.md`

---

## How to Use This File

1. Pick a priority tier to work through in the next session.
2. Start with **Priority 1** — these fill the most important gaps.
3. When a file is created, move its row to the "Already Covered" section.
