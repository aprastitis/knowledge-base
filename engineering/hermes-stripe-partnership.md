---
type: card
status: active
date: 2026-06-16
summary: Hermes + Stripe partnership bringing three new skills for web payments, per-call APIs, and SaaS provisioning
tags: [hermes, stripe, payments, skills, partnership]
related: [engineering/hermes/skills, engineering/hermes/plugins]
sources: [NousResearch/Hermes scan 2026-06-16]
---

# Hermes + Stripe Partnership

## Overview

Hermes announced a partnership with Stripe, adding three new skills to the official catalog for handling payments and monetizing agent capabilities.

## New Skills

### stripe-link-cli
Buy things on the open web using Stripe-powered checkout flows integrated into the agent workflow.

### mpp-agent
Pay-per-call API framework — enables agents to expose capabilities as metered APIs where callers pay per invocation.

### stripe-projects
Provision your own SaaS product using Stripe billing infrastructure. Jumpstart metering, subscriptions, and usage-based pricing.

## Safety Model

Three-layer safety architecture for credential handling:

1. **Primary credentials never enter agent transcripts** — sensitive keys are stripped from conversation history
2. **Configurable human approval gates** — spend limits and action confirmations before executing payment operations
3. **One-time credentials auto-cleaned after use** — temporary auth tokens are destroyed after the operation completes

## Implications

- Agents can now act as payment-enabled autonomous assistants
- mpp-agent enables a new monetization pattern: agents as API products
- Safety model demonstrates credential isolation best practices for agent-tool integration

## Related

- [[engineering/hermes/skills]] — SKILL.md format, authoring standards, platform gating
- [[engineering/hermes/plugins]] — Plugin architecture for extending Hermes capabilities
