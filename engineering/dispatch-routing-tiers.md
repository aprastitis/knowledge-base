---
type: engineering
status: active
date: 2026-05-25
summary: Task complexity-based routing — 5 tiers (Simple/Medium/Heavy/Full/Plan) that determine which AI process and context to use. Prevents over-engineering simple tasks and under-engineering complex ones.
tags: [agentic-coding, engineering-practice, knowledge-management]
related: [[gstack-process-pattern]], [[ask-mode-before-code-mode]], [[scaffolding-for-ai-agents]]
sources: [garrytan/gstack]
---

# Dispatch Routing Tiers

A framework for routing tasks to the right level of AI process and context based on complexity. Simple tasks get simple treatment; complex projects get the full pipeline. No under-engineering of complex work; no over-engineering of simple work.

Source: [garrytan/gstack](https://github.com/garrytan/gstack) — dispatch tier system as part of the gstack OpenClaw integration.

---

## The Problem

Without complexity-based routing, two failure modes dominate:

**Over-engineering simple tasks** — A "fix the typo" request triggers full planning, multi-file analysis, review, and QA. Wasted cycles, slow turnaround.

**Under-engineering complex tasks** — A "build the notification system" request gets a quick implementation without architectural review, security audit, or test planning. Technical debt, missed edge cases, security gaps.

The right routing prevents both.

---

## The Five Tiers

| Tier | When to use | Process | Context |
|------|-------------|---------|---------|
| **Simple** | One-file edits, typos, config changes | Direct implementation | No special context |
| **Medium** | Multi-file features, refactors, approach is clear | Lightweight planning discipline | gstack-lite (5-line plan + self-review) |
| **Heavy** | Specific skill needed (/review, /cso, /qa) | Named skill execution | Full skill context |
| **Full** | Complete features, objectives, projects | Full pipeline | gstack-full (CEO → Eng → Design → Review → Ship) |
| **Plan** | Want to plan without implementing | Planning gauntlet, save plan | gstack-plan (office-hours → autoplan → save) |

---

## Tier Decision Logic

```
Can it be done in Simple?
  → Yes: Do Simple (direct, no extra context)

Does it touch multiple files but approach is obvious?
  → Yes: Do Medium (lightweight plan + self-review)

Does the user name a specific skill (/cso, /review, /qa)?
  → Yes: Do Heavy (run the named skill)

Is it a feature, project, or objective (not a task)?
  → Yes: Do Full (run the full pipeline)

Does the user want to PLAN something without implementing yet?
  → Yes: Do Plan (office-hours → autoplan → save plan)
```

---

## What Each Tier Looks Like

### Simple
**Prompt:** "Fix the typo in README"
**Response:** Direct fix, no planning, no review loop.
**Context:** None extra — just fix it.

### Medium
**Prompt:** "Add authentication to the API"
**Response:** 
1. Read all relevant files
2. Write a 5-line plan: what, why, which files, test case, risk
3. Implement
4. Self-review before reporting done
5. Completion report: what shipped, decisions made, anything uncertain

### Heavy
**Prompt:** "Run a security audit on this repo"
**Response:** Spawn with `/cso` skill. Full OWASP + STRIDE audit. Report with concrete exploit scenarios.
**Context:** Full security skill context loaded.

### Full
**Prompt:** "Build a notifications system end-to-end"
**Response:**
1. Run `/office-hours` — CEO interrogation, design doc
2. Run `/autoplan` — CEO + Eng + Design + DX review
3. Implement approved plan
4. Run `/ship` — tests, coverage, PR
5. Report back: PR URL, key decisions, what shipped

### Plan
**Prompt:** "Help me plan the v2 API redesign"
**Response:**
1. Run `/office-hours` — produce design doc
2. Run `/autoplan` — full review gauntlet
3. Save reviewed plan to `plans/v2-api-redesign-plan.md`
4. Report: plan path, summary, key decisions, recommended next step

**The plan persists.** When the user is ready to build, they trigger a Full session that references the saved plan.

---

## The Key Behavioral Rules

These apply to dispatch routing at the orchestrator level:

1. **Always spawn, never redirect** — When a user asks for a skill, always spawn a session with that skill. Never tell the user to open Claude Code themselves.

2. **Resolve the repo** — If the user names a repo, set the working directory. If unknown, ask.

3. **Autoplan runs end-to-end** — Spawn, let the full pipeline run, report back. The user should never have to leave the conversation to monitor progress.

4. **Append, don't replace** — When spawning in a repo that already has project context, append the tier's context as a new section. Don't replace what already exists.

---

## Relationship to Ask Mode / Code Mode

The `ask-mode-before-code-mode` pattern maps to two tiers:

- **Ask mode** ≈ **Plan tier** or **Medium tier** — understand the problem, write a plan, don't implement yet
- **Code mode** ≈ **Full tier** — implement the approved plan, run review and ship

The gstack tiers add more nuance: Simple (direct) vs Medium (lightweight plan) vs Full (full pipeline) vs Plan (plan-only). The ask/code split is a simplified version of this.

---

## When to Escalate

Escalation happens when a tier turns out to be wrong:

| Starting tier | Turns out to need | Action |
|--------------|-------------------|--------|
| Simple | Medium | Write 5-line plan before continuing |
| Medium | Heavy | Load named skill, run it |
| Medium | Full | Run `/autoplan` then implement |
| Plan | Full | Load saved plan, run full pipeline |

The routing decision is not locked at the start — it evolves as work reveals complexity.

---

## Related

- [[gstack-process-pattern]] — The 6 roles and sprint process that the tiers route between
- [[ask-mode-before-code-mode]] — Simplified two-tier version (plan vs implement)
- [[scaffolding-for-ai-agents]] — Tooling determines outcomes; dispatch routing is a tooling decision
