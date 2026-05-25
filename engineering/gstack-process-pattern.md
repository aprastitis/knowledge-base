---
type: engineering
status: active
date: 2026-05-25
summary: Garry Tan's AI team methodology — 6 roles (CEO/Eng Manager/Designer/QA/Security/Release) chained through a sprint process (Think → Plan → Build → Review → Test → Ship → Reflect)
tags: [agentic-coding, engineering-practice, knowledge-management]
related: [[ask-mode-before-code-mode]], [[advisor-pattern-for-ai-agents]], [[scaffolding-for-ai-agents]], [[vibe-coder-security-checklist]]
sources: [garrytan/gstack]
---

# Gstack Process Pattern

Role-based AI team methodology where different AI agents play distinct engineering roles, chained through a sprint process. Each role has a specific mandate; each process step feeds into the next.

Source: [garrytan/gstack](https://github.com/garrytan/gstack) — Garry Tan's (YC President & CEO) open-source AI coding methodology. 23 skills, 6 roles, process-not-tools philosophy.

---

## The Core Insight

A single builder with the right AI tooling can move faster than a traditional team. The key is not more AI — it's AI with *roles* and *process*. Without structure, AI coding agents produce scope creep, architectural drift, and missed bugs. With roles and process, they produce production-ready code.

The difference between "AI as a faster coder" vs "AI as a virtual engineering team" is discipline and specialization.

---

## The Six Roles

Each role is a distinct AI persona with a specific mandate:

| Role | Persona | Mandate |
|------|---------|---------|
| **CEO** | Strategic thinker | Rethinks the problem. Finds the 10-star product hiding inside the request. Challenges scope. |
| **Eng Manager** | Technical architect | Locks architecture, data flow, edge cases, test matrix. Forces hidden assumptions into the open. |
| **Designer** | Quality auditor | Catches AI slop. Rates design dimensions 0-10. Edits toward a coherent vision. |
| **QA Lead** | Browser-based tester | Opens real browser, clicks through flows, finds and fixes bugs, auto-generates regression tests. |
| **Security Officer** | Threat modeler | Runs OWASP Top 10 + STRIDE audits. Independent finding verification. Concrete exploit scenarios. |
| **Release Engineer** | Shipper | Syncs main, runs tests, audits coverage, pushes, opens PR. Verifies production health. |

**Key principle:** These are *personas*, not separate AI instances. The same AI agent shifts mode depending on which role it's playing.

---

## The Sprint Process

Roles chain in the order a sprint runs:

```
Think → Plan → Build → Review → Test → Ship → Reflect
```

Each step feeds into the next:
- `/office-hours` (CEO) writes a design doc → `/plan-eng-review` (Eng Manager) reads it
- `/plan-eng-review` writes a test plan → `/qa` (QA Lead) picks it up
- `/review` (Staff Engineer) catches bugs → `/ship` (Release Engineer) verifies fixes

**Nothing falls through the cracks because every step knows what the previous step produced.**

---

## The Key Skills

### Think & Plan Phase

**`/office-hours`** — CEO persona. Product interrogation with 6 forcing questions:
1. What specific problem does this solve?
2. Who experiences this problem right now?
3. What is the current workaround?
4. What would a perfect solution look like?
5. What is the smallest version that delivers value?
6. What could go wrong if we build this?

Extracts 5 capabilities the requester didn't realize they were describing. Challenges 4 premises. Generates 3 implementation approaches with effort estimates.

**`/autoplan`** — Runs the full planning gauntlet:
- CEO review (scope, strategic fit)
- Eng review (architecture, data flow, edge cases)
- Design review (coherence, quality bar)
- DX review (developer experience, onboarding)

Output: a reviewed, pressure-tested plan that feeds into implementation.

### Build Phase

**`/design-html`** — Turns a mockup into production HTML. Pretext computed layout (text reflows, heights adjust, layouts dynamic). 30KB, zero deps. Detects React/Svelte/Vue. Smart API routing per design type.

### Review Phase

**`/review`** — Staff Engineer persona. Finds bugs that pass CI but blow up in production:
- Auto-fixes obvious issues
- Flags completeness gaps
- Race conditions, error handling gaps, security surface

**`/cso`** — Chief Security Officer. OWASP Top 10 + STRIDE threat model:
- 17 false positive exclusions
- 8/10+ confidence gate
- Independent finding verification
- Each finding includes a concrete exploit scenario

### Test Phase

**`/qa`** — QA Lead. Opens a real browser, clicks through flows, finds bugs, fixes them with atomic commits, re-verifies. Auto-generates regression tests for every fix.

**`/canary`** — Post-deploy monitoring. Watches for console errors, performance regressions, page failures.

### Ship Phase

**`/ship`** — Release Engineer. Sync main → run tests → audit coverage → push → open PR. Bootstraps test frameworks if none exist.

**`/land-and-deploy`** — Merge PR → wait for CI/deploy → verify production health. One command from "approved" to "verified in production."

### Reflect Phase

**`/retro`** — Weekly engineering retrospective. What shipped, what slowed us down, what to improve next week.

---

## Process Flow Examples

### Simple feature (medium complexity)
```
/office-hours → understand the problem
/autoplan → CEO + Eng review
implement → /review → /ship
```

### Complex feature (full pipeline)
```
/office-hours → produce design doc
/autoplan → CEO + Eng + Design + DX reviews + adversarial review
implement the approved plan
/review → catch bugs
/qa → browser test
/ship → open PR
/land-and-deploy → verify production
```

### Debugging
```
/investigate → systematic root-cause debugging
[traces data flow, tests hypotheses]
Iron Law: no fixes without investigation
Stop after 3 failed fixes
```

---

## Related Patterns in Our KB

- [[ask-mode-before-code-mode]] — Two-step pattern: plan in Ask mode, execute in Code mode. Similar to the Think → Build separation.
- [[advisor-pattern-for-ai-agents]] — Smaller model as executor + larger model as advisor. Role separation applied to model routing.
- [[scaffolding-for-ai-agents]] — Tooling determines outcomes more than model. Gstack is a scaffolding framework.
- [[vibe-coder-security-checklist]] — Pre-launch security audit. Complementary to /cso role.

---

## Applicability to Hermes

Hermes can adopt the *process discipline* without adopting the specific slash commands:

1. **Before building:** Run `/office-hours` equivalent — 6 forcing questions, extract 5 unstated capabilities, challenge 4 premises
2. **Before implementing:** `/autoplan` equivalent — CEO + Eng + Design review of the plan
3. **Before shipping:** `/review` + `/cso` equivalent — bug catch + security audit
4. **After shipping:** `/canary` equivalent — post-deploy monitoring loop

The role persona framing is also useful for multi-agent Hermes workflows: different agents playing different roles (architect vs reviewer vs security auditor) with a defined handoff protocol.

See also: [[engineering/dispatch-routing-tiers]] — how to route tasks of different complexity to the right process level.
