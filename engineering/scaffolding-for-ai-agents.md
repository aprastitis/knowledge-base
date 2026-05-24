# Scaffolding for AI Agents

The tooling around an AI model determines outcomes more than the model itself. This is **scaffolding** — everything from prompt design to CI integration to context management. Good scaffolding makes the model better; bad scaffolding makes it worse.

## The Core Insight

Most people focus on which model to use. The real leverage is in the **system around the model**:
- How context is managed (what does the agent see when it works?)
- How tools are integrated (can it actually do what you need it to do?)
- How feedback loops work (does it learn from errors?)
- How automation is triggered (what happens after it produces output?)

As models get more capable, scaffolding becomes the bottleneck. A model that could do more is held back by scaffolding that doesn't trust it.

## What Good Scaffolding Looks Like

### 1. Context Management
- Keep relevant context in memory; don't rely on the full conversation window
- Give the agent clear, stable goals — not shifting requirements mid-task
- Structure context so the agent can find what it needs without parsing noise

### 2. Tool Integration
- MCP servers for secure access to internal systems (no public exposure needed)
- CI integration for automated testing and validation
- Clear tool interfaces — the agent should know what each tool does without guessing

### 3. Trust the Agent's Long-Task Capability
- Let it run multi-step workflows without interrupting
- Don't micromanage with step-by-step instructions
- When something starts failing repeatedly, that's the signal to upgrade the scaffolding, not to constrain the model

### 4. Security Scanning
- Claude Security feature: scans shared context variables for secrets/leaks before they go to production
- Scan tool call arguments for credential exposure
- Validate outputs before they reach external systems

## The Scaffolding-Versus-Model Relationship

| Scenario | Result |
|----------|--------|
| Strong model + weak scaffolding | Model underperforms; capabilities wasted |
| Good model + good scaffolding | Amplified output; model does what it's capable of |
| Weak model + excellent scaffolding | Surprisingly effective; scaffolding fills capability gaps |

The pattern: **scaffolding determines the ceiling, model determines the floor.**

## Warning Signs of Bad Scaffolding

- Constantly steering the agent away from tool usage
- Needing to break tasks into tiny steps because the agent "can't handle" larger ones
- Same errors repeating because the agent doesn't have context to avoid them
- Prompting that fights the model's actual capabilities

## Relationship to My Work

When I build skills, I'm building scaffolding. The prompts, tool definitions, context management, error handling — all of this determines whether I'm using the model effectively or wastefully.

The Anthropic talk made this explicit: as models get more capable, the scaffolding that "helped" can start holding the model back. The right instinct is to **trust the model more as it improves**, not to add more guardrails.

## See Also

- [[agentic-coding-tools]] — How agentic coding tools use scaffolding
- [[advisor-pattern-for-ai-agents]] — How advisor pattern acts as a form of scaffolding
- [[evals-ai-evaluation-frameworks]] — Evals measure whether scaffolding actually works; the feedback loop for prompt and tool changes
- [[vibe-coder-security-checklist]] — Security scaffolding for AI-assisted development