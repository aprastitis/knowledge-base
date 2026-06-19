---
type: engineering
status: active
date: 2026-06-09
summary: The next evolution beyond harness engineering — designing systems that prompt agents for you, instead of prompting them directly. Five building blocks + external memory, composable into self-running loops.
tags: [engineering-practice, agentic-coding, loop-engineering, automation, multi-agent]
related: [[engineering/harness-engineering]], [[engineering/multi-agent-orchestration-patterns]], [[engineering/skill-trigger-design]], [[concepts/model-context-protocol]], [[engineering/scaffolding-for-ai-agents]], [[engineering/agentic-coding-principles]], [[engineering/ask-mode-before-code-mode]], [[engineering/2026-06-19-engineering-agentic-loop-pattern]]
sources: [https://x.com/addyosmani/status/2064127981161959567 (Addy Osmani, 2026-06-08)]
---

# Loop Engineering

A new engineering discipline that sits one floor above harness engineering. Where harness engineering designs the environment a single agent runs inside, **loop engineering designs the system that orchestrates agents on a schedule** — replacing you as the person who prompts the agent.

Coined by Addy Osmani (2026-06-08), building on earlier work by @steipete and @bcherny (head of Claude Code at Anthropic).

## The Core Shift

**Old model:** You write a good prompt → agent responds → you read → you write the next prompt. One turn after another. The agent is a tool you hold.

**New model:** You design a small system that finds the work, hands it out, checks it, writes down what's done, and decides the next thing. The system pokes the agents instead of you.

@bcherny put it plainly: *"I don't prompt Claude anymore. I have loops running that prompt Claude and figuring out what to do. My job is to write loops."*

@steipete: *"You shouldn't be prompting coding agents anymore. You should be designing loops that prompt your agents."*

## The Six Building Blocks

Both Claude Code and Codex now have all five primitives built in (under different names). This means the loop design is tool-agnostic — the same loop works in either tool.

### 1. Automations (The Heartbeat)

Scheduled tasks that run on a cadence and do discovery/triage autonomously. The automation is what makes a loop an actual loop, not just one manual run.

**Codex:** Automations tab → pick project, prompt, frequency, worktree vs local. Findings go to Triage inbox. Empty runs archive themselves.

**Claude Code:** `/loop` re-runs on a cadence. `/goal` keeps going until a verifiable stopping condition holds — a separate small model (not the one that did the work) checks whether you're done. Also: cron scheduling, lifecycle hooks, GitHub Actions for post-laptop persistence.

**What it replaces:** You manually checking CI failures, open issues, recent commits every morning.

### 2. Worktrees (Parallel Isolation)

Git worktrees let multiple agents work on the same repo simultaneously without file collisions. One agent's edits literally cannot touch another agent's checkout.

**Codex:** Built-in worktree support — multiple threads hit the same repo without bumping.

**Claude Code:** `--worktree` flag opens a session in its own checkout. `isolation: worktree` setting on a subagent gives each helper a fresh checkout that cleans itself up after.

**The constraint:** Your review bandwidth is still the ceiling. Worktrees remove the mechanical collision, but you can't review infinite parallel agents simultaneously.

### 3. Skills (Project Knowledge on Disk)

Skills encode project context — conventions, build steps, "we don't do it like this because of that one incident" — written one time where the agent reads it every run. This stops the re-deriving-of-context-from-zero every session tax.

Both tools use the same format: a folder with a `SKILL.md` inside holding instructions and metadata. Codex auto-fires skills when task description matches. Claude Code invokes them explicitly or auto-matches.

**The intent debt connection:** Without skills, the loop re-derives your whole project from zero every cycle. With skills, it compounds — the knowledge is durable, the loop just picks it up.

**Plugins** package skills (and connectors) together for sharing across repos or team distribution.

### 4. Plugins and Connectors (MCP)

Connectors, built on MCP, let the loop touch your real tools: issue trackers, databases, staging APIs, Slack. Both Codex and Claude Code speak MCP, so a connector written for one usually works in the other.

**What it enables:** A loop that says "here's the fix" becomes a loop that opens the PR, links the Linear ticket, and pings the channel once CI is green. The loop acts inside your actual environment, not just inside the filesystem.

### 5. Sub-agents (Maker vs Checker)

The most structurally important primitive. Split the agent that writes code from the agent that reviews it. The model that wrote the code grades its own homework too nicely.

**Codex:** TOML files in `.codex/agents/` — name, description, instructions, optional model, reasoning effort. Security reviewer can be strong model/high effort. Explorer can be fast/read-only.

**Claude Code:** Subagents in `.claude/agents/`, agent teams that pass work between them. The usual split: one explores, one implements, one verifies against the spec.

**Claude Code's `/goal` under the hood:** A fresh model decides if the loop is done instead of the one that did the work. The maker/checker split applied to the stop condition itself.

### 6. External Memory (The Spine)

A markdown file, Linear board, or any durable store outside the single conversation. The model forgets everything between runs — the memory has to be on disk. The agent forgets, the repo doesn't.

This is the same principle from [[engineering/scaffolding-for-ai-agents]]: long-running agents depend on external memory because the context window resets between runs.

## A Real Loop Example

One thread, composed together:

1. **Automation** runs every morning on the repo
2. Its prompt calls a **triage skill** that reads yesterday's CI failures, open issues, recent commits → writes findings to a markdown file or Linear board
3. For each finding worth doing: opens an **isolated worktree** → sends a **sub-agent** to draft the fix → a second **sub-agent** reviews against project skills and existing tests
4. **Connectors** open the PR, update the ticket
5. Anything the loop can't handle lands in the **triage inbox** for you
6. **External memory** remembers what got tried, what passed, what stayed open → tomorrow's run picks up where today stopped

You designed it one time. You did not prompt any of those steps.

## What the Loop Still Doesn't Do For You

Three problems get **sharper** as the loop gets better, not easier:

### Verification is still on you

A loop running unattended is also a loop making mistakes unattended. The verifier sub-agent is the only reason you can walk away — but "done" is still a claim, not a proof. Your job is to ship code you confirmed works.

### Comprehension debt grows

The faster the loop ships code you didn't write, the bigger the gap between what exists and what you actually understand. A smooth loop just makes it grow faster unless you read what the loop made. ([[engineering/agentic-coding-principles]] Principle 2: Understand and Verify.)

### Cognitive surrender

When the loop runs itself, it's tempting to stop having an opinion and just take whatever it gives back. Designing the loop is the cure when you do it with judgment — and the accelerant when you do it to avoid thinking. Same action, opposite result.

### Token cost risk

Usage patterns can vary wildly if you're token-rich or token-poor. Loops that re-derive context every run burn tokens fast. Skills and external memory mitigate this, but it's not free.

## The Loop Design Paradox

Two people can build the exact same loop and get completely opposite results:

- **One** uses it to move faster on work they understand deeply
- **The other** uses it to avoid understanding the work at all

The loop doesn't know the difference. You do.

That's what makes loop design harder than prompt engineering, not easier. The leverage point moved — from "write better prompts" to "design better systems." And designing a system that produces good outcomes requires **more** understanding, not less.

## Relationship to Harness Engineering

Loop engineering is the direct successor to [[engineering/harness-engineering]]:

- **Harness engineering:** designs the environment a single agent runs inside
- **Loop engineering:** designs the system that orchestrates agents on a schedule, with scheduled automations, spawned helpers, and self-feeding work queues

Addy's own framing: *"Loop engineering sits one floor above the harness. The harness but it runs on a timer, it spawns little helpers, and it feeds itself."*

The five harness artifacts (AGENT.md files, JSON feature lists, session initialization, sprint contracts, structured task templates) all still apply — they're the content layer inside the loop's skills and memory.

## Relationship to Multi-Agent Orchestration

Loop engineering is a practical application of the [[engineering/multi-agent-orchestration-patterns]]:

- **Worktrees** enable the concurrent/fan-out pattern without collision
- **Sub-agents** implement the handoff and magentic (orchestrator-worker) patterns
- **Skills** are the shared context layer across agents
- **External memory** is the shared state that persists across the multi-agent system

The loop is the architecture; the patterns are the building blocks.

## Key Takeaway

> Build the loop. But build it like someone who intends to stay the engineer, not just the person who presses go.

The engineers winning aren't just writing better prompts. They're designing the system that produces the work — and they stay in the loop because they designed it, not because they're the only one who can run it.
