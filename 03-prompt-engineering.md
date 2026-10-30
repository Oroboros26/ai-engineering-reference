# Prompt Engineering

Techniques for writing prompts that get reliable, high-quality outputs.

---

## Anatomy of a Prompt

```
┌─────────────────────────────────────────┐
│  SYSTEM PROMPT                          │  ← Role, rules, persona, format rules
│  "You are a SQL expert..."              │
├─────────────────────────────────────────┤
│  USER TURN                              │  ← Task, context, examples
│  "Write a query to find top customers"  │
├─────────────────────────────────────────┤
│  ASSISTANT PREFILL (optional)           │  ← Forces output to start a certain way
│  "```sql\n"                             │
└─────────────────────────────────────────┘
```

**Key rule:** Put stable instructions in the system prompt, dynamic content in the user turn.

---

## Core Techniques

| Technique | What It Is | When to Use |
|---|---|---|
| **Zero-shot** | No examples, just instructions | Simple, well-defined tasks |
| **Few-shot** | 2–5 input/output examples | Pattern matching, classification, formatting |
| **Chain-of-Thought (CoT)** | Ask model to "think step by step" | Math, logic, multi-step reasoning |
| **Self-consistency** | Sample N outputs, take majority answer | High-stakes reasoning (expensive) |
| **ReAct** | Reason + Act in a loop with tools | Agentic tasks, tool use |
| **Role prompting** | Assign an expert persona | Quality uplift, tone control |
| **Structured output** | Force JSON/XML output schema | Downstream parsing, reliability |

---

## Few-Shot Example Template

```
Here are examples of the task:

Input: <example_input_1>
Output: <example_output_1>

Input: <example_input_2>
Output: <example_output_2>

Now complete:
Input: <actual_input>
Output:
```

---

## XML Tags (Claude-Specific)

Claude is trained to respect XML-style delimiters — use them to separate content types:

```xml
<instructions>
  Summarize the following document in 3 bullet points.
</instructions>

<document>
  {{paste document here}}
</document>

<format>
  Return as a JSON array of strings.
</format>
```

**Useful tag names:** `<instructions>`, `<context>`, `<examples>`, `<document>`, `<query>`, `<format>`, `<thinking>`

---

## Chain-of-Thought Patterns

```
Minimal CoT:     "Think step by step before answering."
Explicit CoT:    "First reason through the problem in <thinking> tags,
                  then provide your final answer in <answer> tags."
Zero-shot CoT:   "Let's think about this carefully."
```

For Claude, extended thinking (a model feature) provides native CoT — enable with `thinking: {type: "enabled", budget_tokens: N}`.

---

## System Prompt Best Practices

- **Lead with role:** "You are a {role} that {primary responsibility}."
- **State format rules early:** "Always respond in JSON. Never use markdown."
- **Define scope:** "Only answer questions about {topic}. Decline anything else."
- **Keep it under 500 tokens** unless you have complex rules — excess tokens add cost and noise.

---

## Do / Don't Quick Reference

| Do | Don't |
|---|---|
| Be specific and concrete | Say "respond well" — define what "well" means |
| Use examples for format | Assume the model knows your internal jargon |
| Put constraints in system prompt | Repeat instructions every turn |
| Use delimiters to separate sections | Let user input blend into instructions |
| Ask for reasoning before answer | Ask leading questions that bias the answer |
| Test with adversarial inputs | Ship without testing edge cases |

---

## Prompt for Structured JSON Output (Claude)

```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    system="You are a data extractor. Always respond with valid JSON only.",
    messages=[{
        "role": "user",
        "content": "Extract: name, date, amount from: 'John paid $50 on Jan 3rd'"
    }]
)
```

**See also:** [04-context-engineering.md](04-context-engineering.md) for handling large inputs, [07-ai-elements.md](07-ai-elements.md) for tool use.
