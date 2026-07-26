---
type: engineering
status: active
date: 2026-06-19
summary: The inner-loop pattern for an agent task — gather context → act → verify → repeat — with the controls, gates, and failure modes both Anthropic and OpenAI engineers agree on. Complements [[engineering/loop-engineering]] (the outer scheduling loop) and [[engineering/harness-engineering]] (the agent's environment).
tags: [engineering-practice, agent-design, agentic-coding, loop-engineering, hermes, MiniMax, context-engineering, multi-agent]
related: [[engineering/loop-engineering]], [[engineering/harness-engineering]], [[engineering/multi-agent-orchestration-patterns]], [[engineering/progressive-disclosure-pattern]], [[engineering/skill-trigger-design]], [[engineering/hermes/skills]], [[engineering/hermes/architecture]], [[engineering/async-subagents]], [[concepts/context-engineering]], [[engineering/agentic-coding-principles]], [[engineering/scaffolding-for-ai-agents]], [[raw/2026-06-19-raw-claude-agentic-loop-design-report]]
sources: [https://claude.ai/public/artifacts/72bd1d35-98b4-466b-81e4-71cc25aece54 (Claude research synthesis, 2026-06-19), https://www.anthropic.com/engineering/building-effective-agents, https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents, https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk, https://openai.github.io/openai-agents-python/]
---

# Agentic Loop Pattern

The **inner loop** an agent runs to complete a single task: *gather context → take action → verify work → repeat*. This is the layer below [[engineering/loop-engineering]] (which designs the *outer* system that schedules agents) and a sibling of [[engineering/harness-engineering]] (which designs the agent's environment).

Both Anthropic and OpenAI engineering converged on this pattern in 2024–2026, and the Claude research report that prompted this card — `raw/2026-06-19-raw-claude-agentic-loop-design-report.md` — synthesizes their guidance with what we already know about Hermes Agent (Nous) and MiniMax as the engine. This card is the curated distillation; the raw report is the source.

## The Canonical Loop

The shape every successful agent loop takes, in Anthropic's words from *Building agents with the Claude Agent SDK*:

> *"Agents often operate in a specific feedback loop: gather context → take action → verify work → repeat."*

The **verify** step is what separates real agents from chatbots — it's where most of the quality comes from. A loop that gathers and acts without verifying is just an expensive autocomplete.

Hermes v0.18.0 turned this principle into a first-class completion mechanism: `/goal` accepts a completion contract, coding work records verification evidence, and the loop judges that evidence before stopping. See [[engineering/hermes/2026-07-26-hermes-v0-18-judgment-release]].

OpenAI's *Practical Guide to Building Agents* says the same thing differently: *"This concept of a while loop is central to the functioning of an agent… run multiple steps until an exit condition is met."* Exit conditions are either a structured final-output type, or `max_turns`.

**The harness is a dumb loop.** Barry Zhang (Anthropic) at AI Engineer Summit 2025: an agent is *"models using tools in a loop"* and *"the loop itself is mechanically simple (often just a while loop); the intelligence lives in the model, not the harness."* Don't over-engineer the harness. Most of the work is in the model + the description/prompt + the tools, not in custom loop code.

### The Five-Stage Variant

The same loop, named with one extra stage for code-oriented work — from Anatoli Kopadze (2026-06-20, 6,759 likes — `raw/2026-07-16-raw-anatoli-kopadze-loops.md`):

```
DISCOVER  →  work out what needs doing
PLAN      →  decide how to do it
EXECUTE   →  do the work
VERIFY    →  check it against the goal
ITERATE   →  not there yet? feed the result back in and repeat
```

Three of these five (verify, state, stop) do the real work and are where loops break — Kopadze's framing. The 4-stage Anthropic *gather → act → verify → repeat* shape is a compressed form of the same skeleton; the 5-stage form is more useful for code loops because it splits *discover* (gather context — search, read, just-in-time retrieval) from *plan* (decide what to do next) and makes both explicit. Both framings are valid; don't treat the stage count as load-bearing.

## The Activation Gate (When to Loop)

The most important design rule from both labs: **don't loop for everything.** Use an agent loop only when the task is:

1. **Ambiguous** — the decision tree isn't pre-mappable. If you can write a flowchart, just write a script.
2. **Valuable** — the task justifies the token cost. Multi-agent alone is ~15× the tokens of a normal chat (Anthropic's own number).
3. **Verifiable** — you can tell when the output is correct, either by an objective test or by a separate critic. If neither is possible, the loop can't actually evaluate its own progress.

Zhang's three-question gate: *Is the decision tree un-mappable? Is the task worth the tokens? Is the output verifiable?* If any answer is no, **answer directly without a loop**.

This gate is what keeps an "agentic-loop" skill dormant in ordinary chat. Put the gate in the YAML `description` (per [[engineering/progressive-disclosure-pattern]] and [[engineering/skill-trigger-design]]), and only the description lives in context until a task matches.

## The Six Controls (Hard-Won Lessons)

The two labs converged on the same set of engineering controls. These are the things you actually tune.

### 1. Stopping conditions

The harness terminates when any of these hold:
- **Completion signal** — the agent returns a plain text response with no tool calls. Both Claude Code and Hermes use this as the implicit "done" signal.
- **Max iterations / max turns** — Hermes default: `agent.max_turns = 90` (configurable, not immutable). OpenAI Agents SDK: `max_turns` raises `MaxTurnsExceeded`.
- **Cost / token budget** — wall on spend.
- **Error threshold** — N consecutive failures, give up.

For *self-correction* loops specifically, cap revisions at **2–3**. Beyond that, reflection loops cycle indefinitely and degrade quality through over-editing. Other strategies: quality threshold, convergence detection (revisions produce minimal change), or external verification (test/tool confirms correctness).

### 2. Self-verification with a separate critic

The most important control, and the most often skipped. From production write-ups (2025–2026): a single model acting as its own generator and critic *"tends to reproduce the same reasoning structure across iterations, with little meaningful correction."* Anthropic's *Harness design for long-running apps* notes agents *"tend to respond by confidently praising the work — even when… the quality is obviously mediocre."*

**Practical rules:**
- Use a *separate* critic prompt/role, not inline self-review.
- Prefer **execution-based feedback** (run the tests, run the code, re-query) over open-ended critique. It's the strongest signal.
- Use **binary checks**, not "is this good?" — pass/fail is more reliable than subjective scoring.
- Cap at one revision cycle before escalating.

### 3. Context management (three levers)

Anthropic's *Effective context engineering* prescribes three levers for long-horizon work — all three show up in Hermes and OpenAI's runtimes.

- **Compaction** — summarize a conversation near the context-window limit and reinit with the summary. Hermes: preflight at >50% context, gateway auto-compress at >85%. Claude Code preserves *"architectural decisions, unresolved bugs, and implementation details while discarding redundant tool outputs,"* then continues *"with this compressed context plus the five most recently accessed files."* Lossiness is a spectrum, not a binary — tune for recall first, precision second.
- **Structured note-taking** — write persistent notes outside the window (`NOTES.md`, `todo.md`, `claude-progress.txt`, `feature-list.json`) and re-inject on demand. Anthropic cites Claude playing Pokémon maintaining tallies across thousands of steps. Manus's pattern: agents *"recite objectives into the end of context"* via `todo.md` to fight the lost-in-the-middle problem, and they keep failed actions visible because *"erasing failure removes evidence."*
- **Multi-agent / sub-agent isolation** — each sub-agent explores in a clean context window and returns only a condensed summary (1,000–2,000 tokens). See [[engineering/multi-agent-orchestration-patterns]] and [[engineering/hermes/architecture]].

For very long horizons, Anthropic's *Effective harnesses for long-running agents* (Nov 2025) adds an **initializer + coding agent** pattern: initializer sets up `init.sh`, `claude-progress.txt`, an initial git commit, `feature-list.json`; subsequent sessions make incremental progress *"one feature at a time"* and leave structured artifacts for the next session.

The deeper principle: **context is a finite resource that degrades** — "context rot" (Chroma Research, July 2025) shows recall degrades as tokens grow, even below the hard limit. The cure is not "bigger context" but tighter selection.

### 4. Sub-agents (orchestrator-workers)

Anthropic's *How we built our multi-agent research system* (June 2025): a lead agent coordinates and delegates to specialized sub-agents that run in parallel, each with its own context window. Numbers: Claude Opus 4 lead + Sonnet 4 sub-agents beat single-agent Opus 4 by **90.2%** on Anthropic's internal research eval, at **~15× the tokens**.

The hard-won lesson is the **delegation contract**: each sub-agent needs four things, and missing any one causes drift:
1. An **objective** (what does done look like?)
2. An **output format** (how should the result be structured?)
3. **Tool/source guidance** (which tools, which sources to consult)
4. **Clear task boundaries** (what's in scope, what's not)

Embed **effort-scaling rules** in the prompt (1 agent for simple fact-finding, 2–4 for comparisons, 10+ for complex research) because the lead otherwise over-scales.

Anthropic's own caveat: *"domains that require all agents to share the same context or involve many dependencies between agents are not a good fit for multi-agent systems today."* Multi-agent is best for breadth-first, parallelizable work and worse for tightly-coupled tasks like most coding.

Hermes's `delegate_task` implements exactly this pattern, with isolated child contexts and 50-iteration subagent budgets ([[engineering/hermes/architecture]]). Hermes added async subagents in June 2026 ([[engineering/async-subagents]]), so the parent no longer blocks.

### 5. Tool design within loops

Anthropic's *Writing effective tools for agents*:
- **Few, well-scoped tools** targeting high-impact workflows. Vercel removed 80% of agent tools and got *better* performance.
- **Consolidate multi-step operations** into single tools (`schedule_event` not `list_users`+`list_events`+`create_event`).
- **Return only high-signal results** (`search_logs` not `read_logs`).
- **Use namespacing** to disambiguate (`asana_search`, `jira_search`).

The litmus test: *"If a human engineer can't definitively say which tool should be used in a given situation, an AI agent can't be expected to do better."* If tool clarity fails, split the agent.

### 6. Error handling & recovery

Keep failed actions visible — they're evidence for the model. OpenAI's SDK supports retries, max-turn caps, guardrail tripwires (input/output/tool), and human-in-the-loop approval (`needs_approval=True` pauses the run as a *resumable* interruption; treat approvals as paused runs, not as new turns). Anthropic recommends formal rules for repeated failures and tool/credential checks before irreversible actions.

## The Seven Failure Modes (Name Them to Defend Against Them)

Both labs and the practitioner literature converge on the same named failure modes. Defending against them is what separates a working loop from a runaway one.

| Failure mode | What happens | Defense |
|---|---|---|
| **Infinite loop / runaway iteration** | Agent never declares done, burns budget | Always set max iterations; cap revisions at 2–3; convergence detection |
| **Drift** | Goal recedes as context grows; agent solves a different problem than asked | Recite goal at end of context each iteration (`todo.md`); for sub-agents, give explicit boundaries; note that drift is *hard to detect, late to surface* |
| **Premature stopping** | Agent declares done with bad output | Never accept "done" without verification; for coding → tests must pass; for research → criteria checklist met |
| **Context overflow / rot** | Recall degrades before the hard limit hits | Compaction + note-taking + sub-agent isolation; keep tool outputs lean |
| **Hallucinated completion** | Model confidently praises mediocre work | Require execution-based or separate-critic verification, not inline self-approval |
| **Hallucinated tool selection** | Agent picks the wrong tool from an overlapping set | Few, clearly-named, non-overlapping tools; split agents when tool clarity fails |
| **Ralph Wiggum loop** 🆕 | Agent decides it's done too early, exits on a half-finished job; the loop keeps billing and producing nothing. Coined by Geoffrey Huntley (per Kopadze, 2026-06-20). | Hard gate that can *fail* the work, not just succeed — every loop needs a verifier strong enough to reject bad output. Worse than "premature stopping" because the loop *appears* to be working (it just doesn't) and only the bill tells you something's wrong. |

## The Cost That Matters

Most loop cost talk focuses on *tokens spent per run*. That's the wrong metric. The metric that actually determines whether a loop pays for itself is **cost per accepted change** (per Kopadze, 2026-06-20):

```
ROUGH COST OF ONE LOOP
single agent, one medium task:       ~50,000 – 200,000 tokens
context re-sent every iteration:     grows each pass
maker + checker pattern:             ~doubles the bill (two reads of the work)
a fleet of agents in parallel:       multiply all of the above
```

If your loop yields 10 results and you reject 6, you're doing the review work the loop was supposed to save — and the loop costs more than it returns. **Below a 50% accept rate, a loop costs more than it gives back.** The 50% rule of thumb is from practitioner lore, not a controlled study — treat it as a tripwire, not a law.

This also reframes the Ralph Wiggum failure mode above as a *cost* failure, not just a correctness failure: a silent loop that produces nothing is more expensive than a noisy loop that fails loudly, because only the noisy one teaches you to stop. If your loop can't surface its own waste, instrument the spend (per [[engineering/agent-observability-opentelemetry]]) so you can see when accept rate is silently collapsing.

**Practical leverage points when accept-rate drops or cost spikes:**

- Lower the default iteration/budget ceiling (e.g. cap at 8, not 90).
- Switch the boring steps to a cheaper model (cf. maker-checker split in §4 below and [[engineering/2026-06-27-hermes-moa-virtual-models]]).
- Strengthen the verifier so the loop fails loudly on bad output instead of accepting it.
- Add a convergence check: stop when revisions produce minimal change, not just when the budget runs out.

## Hermes + MiniMax Specifics

Hermes is built around this loop natively ([[engineering/hermes/architecture]]), with these specifics worth knowing if you're designing a meta-skill or debugging behavior:

- **Iteration budget** is a configurable `IterationBudget` (default 90, not immutable). Sub-agents get independent budgets capped at `delegation.max_iterations` (default 50). Total parent+child work can exceed the parent's cap.
- **Compaction triggers**: preflight at >50%, gateway auto-compress at >85%. During compression, memory flushes to disk, middle turns summarize, last 20 messages preserved, tool-call/result pairs kept together, new session-lineage ID generated. User command: `/compress`.
- **System-prompt stability**: the system prompt doesn't change mid-conversation (for prompt-cache stability). Per-turn state (budget warnings, context pressure) is appended to the *user* message, not the system prompt.
- **Skills are first-class** ([[engineering/hermes/skills]]): progressive-disclosure `SKILL.md` files in `~/.hermes/skills/`, loaded via `skills_list()` (~3k tokens) → `skill_view(name)` → `skill_view(name, path)`. Every installed skill is automatically a slash command. Hermes self-authors skills after a complex task (5+ tool calls), after finding a working path through errors, after user correction, or on discovering a non-trivial workflow.
- **Plugin hooks**: `pre_tool_call`, `post_tool_call`, `pre_llm_call` (the only one that can inject context, once per turn before the tool loop), `post_llm_call`, `on_session_start`, `on_session_end`.

**MiniMax as engine** ([[concepts/context-engineering]]-adjacent): MiniMax-M2/M2.7/M3 are *interleaved-thinking* models. They reason in `<think>...</think>` between tool calls in a **plan → act → reflect** loop. The model card is emphatic: *"MiniMax-M2 is an interleaved thinking model… you must ensure that the historical content is passed back in its original format. Do not remove the `<think>...</think>` part, otherwise, the model's performance will be negatively affected."*

MiniMax's own ablation: retaining vs. discarding prior-round thinking shows **BrowseComp 44.0 vs. 31.4 (+40.1%)**, **Tau² 87 vs. 64 (+35.9%)**, **GAIA 75.7 vs. 67.9 (+11.5%)**. Within a single turn, prior tool-call reasoning *must* be retained; across new turns, old reasoning can be dropped to save context. M2.1's eval scaffold retains the first AI response, the last five, and tool outputs when usage exceeds 30% of the window.

## Practical Synthesis — The Loop Skeleton

If you're building a meta-skill, a sub-agent template, or a procedural playbook for a complex task, this is the skeleton both labs effectively prescribe:

1. **Plan** — restate goal, define explicit success criteria, set a hard iteration/budget ceiling (lower than the framework default for bounded tasks), write a `todo.md`/progress note.
2. **Gather context** — just-in-time retrieval via `grep`/`read`/etc. Pull only high-signal tokens. Don't front-load.
3. **Act** — prefer few, well-scoped tools; one logical step at a time. If multiple tools could apply, the description should disambiguate.
4. **Verify** — run an objective check (tests, re-query, schema validation) or a separate critic pass. Cap revisions at 1–3.
5. **Manage context** — at compaction thresholds, summarize to a progress artifact and continue. For breadth, delegate to a sub-agent with the four-part contract and ingest only its summary.
6. **Stop** — emit a plain-text completion only when success criteria are *checked* as met. Otherwise stop at the budget ceiling and report state honestly.

## Build-Evaluate-Iterate

Both labs are emphatic: start with evaluation. Find capability gaps by running representative tasks, build skills/playbooks incrementally, watch full trajectories (not just final scores — *"diagnostics on the trace, not the score"*), and iterate.

**Benchmarks that should change your design:**
- Skill activates during ordinary conversation → tighten the `description`; it's the router.
- Loops run long or cost spikes → lower the default iteration/budget ceiling, enforce convergence detection.
- Agent declares done but output is wrong → strengthen verification (add execution-based checks or a separate critic).
- Sub-agents duplicate or drift → tighten the four-part delegation contract, add effort-scaling rules.
- Long tasks lose the thread → strengthen structured note-taking and progress artifacts.

## Caveats Worth Carrying

- **Some claims are vendor-reported.** The "+40.1% BrowseComp" and "90.2% multi-agent uplift" numbers come from vendor/lab evals; treat as directional, not independently audited.
- **Hermes is fast-moving.** At v0.16.0 (June 2026) some doc pages lag the code (e.g., the delegation page still said subagents were synchronous a week after async subagents shipped). Verify against the running version before relying on a doc claim.
- **Multi-agent is expensive.** ~15× tokens for ~90% better research results. Only worth it for high-value, parallelizable, breadth-first work — explicitly *not* for tightly-coupled work like most coding.
- **The "dumb loop" framing can mislead.** The harness is dumb but the **prompt**, **tools**, and **skill descriptions** are not. Most production failures are prompt/tool/description problems, not loop-code problems.

## Relationship to Existing Cards

- **[[engineering/loop-engineering]]** — outer loop, the system that schedules agents. This card is the *inner* loop, the per-task iteration cycle.
- **[[engineering/harness-engineering]]** — the agent's environment (constraints, AGENT.md, eval harness, plugin hooks). The harness is *what* the loop runs inside; this card is *how* the loop itself iterates.
- **[[engineering/multi-agent-orchestration-patterns]]** — the five patterns (sequential, concurrent, group chat, handoff, magentic). The sub-agent control above is the magentic / orchestrator-worker pattern.
- **[[engineering/progressive-disclosure-pattern]] + [[engineering/skill-trigger-design]]** — the description field is the activation gate; the body is the loop skeleton. Skill authoring is loop authoring at the meta level.
- **[[concepts/context-engineering]]** — the discipline the three context levers (compaction, note-taking, sub-agents) implement.
- **[[engineering/hermes/architecture]] + [[engineering/hermes/skills]] + [[engineering/async-subagents]]** — the concrete Hermes implementation of everything above.
- **[[engineering/scaffolding-for-ai-agents]]** — the principle: tooling around the model determines outcomes more than the model itself. The loop pattern is one expression of this.

## See Also
- `raw/2026-06-19-raw-claude-agentic-loop-design-report.md` — the full Claude research synthesis (38 KB, all source links preserved).
- `raw/2026-07-16-raw-anatoli-kopadze-loops.md` — full verbatim of @AnatoliKopadze's 2026-06-20 long-form thread (6,759 likes, 1,026 reposts); the source for the 5-stage variant (§1), the "Ralph Wiggum loop" failure mode (§5), and the cost-per-accepted-change metric (§6) added in this revision.
- Anthropic, *Building Effective Agents* (Dec 2024) — the foundational text.
- Anthropic, *Effective context engineering for AI agents* — the three-lever context discipline.
- OpenAI, *A Practical Guide to Building Agents* (2025; updated April 2026) — the parallel guide from the other lab.
