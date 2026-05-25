---
type: engineering
status: active
date: 2026-05-20
summary: How to test AI outputs systematically; eval types, metrics, frameworks, getting started with evals
tags: [engineering-practice, agent-design]
related: [[scaffolding-for-ai-agents]], [[spec-driven-development]]
sources: [internal]
---

# Evaluations / Evals

## What are Evals?

**Evals** (evaluations) are systematic ways to measure whether an AI system — or a change to it — is actually working. They're the feedback loop that separates "we think this works" from "we measured it works."

Think of them as unit tests for AI outputs. Instead of checking if a function returns `true`, you check if your LLM's response is accurate, helpful, factual, or safe.

## Why Evals Matter

| Without Evals | With Evals |
|---------------|------------|
| Ship a prompt change and cross your fingers | Confidence that changes actually improve the system |
| Users report issues you can't reproduce | Regression detection before users find bugs |
| Can't tell if RAG retrieval is working | Measurable retrieval quality you can track over time |
| "Is the new model better?" → shrug | Data-driven model selection decisions |
| No way to know if you're moving forward or backward | A feedback loop that actually works |

## Types of Evals

### 1. Exact Match / Pattern-Based
Fast, deterministic, first line of defense. Catches obvious errors before compute is wasted on fancy evals.

- **Exact match**: predicted == expected. For structured outputs (labels, entity extraction, SQL)
- **Regex/parsing checks**: Validate JSON schema, required fields, format adherence
- **Substring/keyword matching**: Check for required elements in generated text
- **Length constraints**: Token counts, character limits

Best for: classification, extraction, structured output, code compilation checks.

### 2. Semantic Similarity
When exact match is too strict but you need objectivity.

- **Embedding cosine similarity**: Compare output embeddings to reference. Threshold typically 0.7–0.9
- **BERTScore**: Contextual embeddings comparing tokens. F1 variant most useful — good for translation, summarization
- **BLEU/ROUGE**: N-gram overlap. BLEU for generation, ROUGE for summarization. High scores don't guarantee quality — use with caution

Best for: summarization, paraphrase detection, semantic search quality.

### 3. LLM-as-Judge
Use a strong model to evaluate outputs from your production model. Most popular approach in practice.

**Single-output scoring:**
```
Rate this customer support response on a 1–5 scale for:
- Empathy (addresses customer emotion)
- Accuracy (correct information)
- Completeness (answers all questions)

Response: {output}
Return JSON: {"empathy": X, "accuracy": Y, "completeness": Z}
```

**Pairwise comparison:** Which output is better? More robust than absolute scoring.
- Randomize order — judges bias toward the second response
- Run both permutations

**Critique-based:** Ask the model to identify specific issues.
```
List factual errors in this summary:
Original: {source}
Summary: {output}
```

**Key considerations:**
- Use structured outputs (JSON) for consistent parsing
- Temperature=0 for reproducibility
- Claude/GPT-5 as judges correlate ~0.85–0.9 with human ratings on many tasks
- Validate your judge against human labels on a subset first
- Judge models have biases: position bias, verbosity bias, self-preference

Best for: open-ended generation, summarization quality, user-facing responses.

### 4. Retrieval Metrics (RAG Systems)
Evaluate both retrieval and generation separately.

**Retrieval-specific:**
- **Precision@K**: Of K retrieved docs, what % are relevant?
- **Recall@K**: Of all relevant docs, what % did you retrieve?
- **MRR** (Mean Reciprocal Rank): 1/rank of first relevant result. Rewards early relevant hits
- **NDCG** (Normalized Discounted Cumulative Gain): Accounts for position AND relevance degree

**Context quality:**
- **Context relevance**: Does retrieved context actually help answer the query?
- **Answer faithfulness**: Is the generated answer grounded in the retrieved context (no hallucinations)?

### 5. Task-Specific Metrics

**Code generation:**
- **Pass@K**: % of problems where at least 1 of K samples passes tests
- Unit test passage rates
- Compilation success
- Execution time, cyclomatic complexity

**Classification:**
- Accuracy, precision, recall, F1
- Confusion matrix analysis
- Per-class breakdowns
- Calibration (are confidence scores meaningful?)

**Summarization:**
- **Coverage**: Does summary include key information from source?
- **Consistency**: Factual alignment with source
- **Coherence**: Readability and flow
- Use ROUGE + LLM judges + human spot-checks

**Structured extraction:**
- Exact match on extracted entities
- Partial credit for partially correct extractions
- Schema compliance rate
- Hallucination rate (extracted things not in source)

## Eval Frameworks & Tools

| Tool | What it does |
|------|--------------|
| **EleutherAI lm-evaluation-harness** | Open-source benchmark harness for LLMs (MMLU, HELM, BIG-Bench) |
| **Ragas** | RAG-specific evaluation metrics (faithfulness, answer relevance, context precision) |
| **DeepEval** | Open-source LLM eval framework (similar to unit tests for LLM outputs) |
| **Promptoo** | Evaluation pipeline for LLM applications |
| **Arize AI** | LLM observability and evaluation platform |
| **Evidently AI** | Open-source ML/LLM evaluation library |
| **AlpacaEval** | Reference-based LLM evaluation using LLM-as-judge |
| **MT-Bench / BIG-Bench** | Multi-turn dialogue benchmarks |
| **OpenAI Evals** | OpenAI's eval framework for benchmarking AI outputs |

## Eval vs. Benchmark

- **LLM benchmarks** (MMLU, HELM, BIG-Bench): Evaluate the model's raw abilities — coding, reasoning, factual knowledge. Used by researchers to compare models on leaderboards.

- **Evals** (in agentic/production context): Evaluate whether a specific system — prompt + model + retrieval + tools — actually works for your use case. Task-oriented, not model-oriented.

## Getting Started

1. **Pick one critical path** — the most important thing your system does
2. **Build 50 test cases** — real inputs your users actually send
3. **Pick 2–3 metrics** — not everything at once
4. **Run before each deployment** — catch regressions automatically

The hardest parts:
- Choosing metrics that matter (not just easy-to-measure ones)
- Keeping test sets fresh and representative
- Actually using eval results to make decisions
- Balancing quality, cost, and latency

## Related Cards

- [[agentic-coding-tools]] — Testing is part of the autonomous coding workflow
- [[advisor-pattern-for-ai-agents]] — Model selection is a routing decision that evals inform
- [[scaffolding-for-ai-agents]] — Tooling around the model determines outcomes; evals measure whether that tooling works
- [[hermes-three-tier-memory]] — Memory tiering affects eval design (what to test at each layer)
- [[anthropic-building-skills-guide]] (raw) — Anthropic's guide covers testing patterns for skills

## Sources

- [Futurice: AI Evals Practical Guide](https://www.futurice.com/blog/ai-evals-practical-guide-part-1) (Jan 2026)
- [Arize AI: The Definitive Guide to LLM Evaluation](https://arize.com/llm-evaluation/) (Apr 2026)
- [Evidently AI: LLM Evaluation Beginner's Guide](https://www.evidentlyai.com/llm-guide/llm-evaluation)
- [Evidently AI: LLM Evaluation Metrics](https://www.evidentlyai.com/llm-guide/llm-evaluation-metrics)
- [Freeplay: LLM Evaluation for AI Products](https://freeplay.ai/blog/llm-evaluation)
- [Shakudo: Evaluating LLM Performance at Scale](https://www.shakudo.io/blog/evaluating-llm-performance)