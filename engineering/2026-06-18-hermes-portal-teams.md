---
type: engineering
status: active
date: 2026-06-18
summary: Hermes Portal team billing — shared credit pool, per-member spend caps, Spend Intelligence dashboard, multi-team membership, single billing owner
tags: [hermes, billing, enterprise, agent-framework, team-deployment, cost-management]
related: [hermes/stripe-partnership, hermes/profiles, hermes/architecture, async-subagents, multi-agent-orchestration-patterns]
sources: [https://x.com/NousResearch/status/2067337553624125621, https://t.co/HDNJIdAWm4]
---

# Hermes Portal Teams — Team Billing

## What It Is

Hermes Portal now supports team-based billing and cost management. This is the first org-level deployment feature for Hermes.

### Core Features

1. **Shared Credit Pool** — Org-level credit balance shared across all members, not per-seat subscriptions
2. **Per-Member Spend Caps** — Set individual spending limits per team member
3. **Spend Intelligence Dashboard** — Granular visibility into spend per member and per team
4. **Multi-Team Membership** — Members can belong to multiple teams and switch between them
5. **Single Billing Owner** — One owner per org, credits flow from that owner

## Why It Matters

This is Hermes going from "personal AI agent" to "org-level AI infrastructure." Key implications:

- **Deploy Hermes at scale** — team leads can provision Hermes for their org without per-seat billing overhead
- **Cost control** — spend caps prevent runaway usage in multi-agent deployments
- **Cross-team visibility** — finance/ops can see exactly who's spending what
- **Multi-team flexibility** — one person, multiple team contexts (useful for contractors, cross-functional leads)

## Relationship to Existing Hermes Features

- **Profiles** (`hermes/profiles`) — multi-instance isolation; team billing complements this with org-level cost visibility
- **Kanban** (`hermes/kanban`) — multi-agent task board; team billing makes Hermes deployments to kanban teams economically scalable
- **Stripe partnership** (`hermes/stripe-partnership`) — payment infrastructure; team billing builds on this

## KB Relevance

Not currently covered in the Hermes documentation cards. Added to `engineering/hermes/` as a product update and enterprise deployment reference.
