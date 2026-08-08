---
type: engineering
status: active
date: 2026-08-03
summary: Hermes Agent is now a hosted product. Pick a model and a server size, get a live agent in 60 seconds. Desktop discovers Cloud instances; team provisioning is built in; rabbitOS ships it preinstalled.
tags: [hermes, cloud, distribution, hosting, nous-portal, desktop, rabbit, team-provisioning]
related:
  - engineering/hermes/2026-08-02-hermes-v0-19-quicksilver-release
  - engineering/hermes/2026-08-03-hermes-v0-20-herald-release
  - engineering/hermes/messaging
  - engineering/hermes/profiles
  - engineering/hermes/architecture
  - engineering/hermes/2026-06-18-hermes-portal-teams
  - engineering/hermes/README
sources:
  - https://x.com/NousResearch/status/2074878754485043333
  - https://x.com/NousResearch/status/2075675120442486931
  - https://x.com/NousResearch/status/2075675708567793664
  - https://x.com/NousResearch/status/2075698031844818990
  - https://x.com/NousResearch/status/2074878756477411523
  - https://nousportal.com
---

# Hermes Cloud — Hosted Distribution Launch

Launched July 8, 2026 (announcement tweet at 15:30 UTC), Hermes Cloud is the shift from "you run Hermes" to "Hermes runs on Nous infrastructure, you talk to it." Before this, every Hermes install was a local artifact: a CLI on a developer laptop, a Docker container, or a self-hosted gateway. Cloud is the first officially hosted, multi-tenant deployment, billed through Nous Portal and surfaced as a first-class endpoint in the Hermes Desktop app.

The headline from the launch tweet: **"Pick a model and a server size. Two clicks and 60 seconds later, your agent is live. Running a team? Spin up agents for everyone."** The provisioning claim is not a marketing approximation — the announcement frames it as "two clicks and 60 seconds" and the surface area was deliberately scoped to make that real.

## What Hermes Cloud Actually Is

Hermes Cloud is *not* a thin wrapper around the open-source Hermes binary. It is a hosted control plane that:

1. Provisions a Hermes Agent instance on Nous infrastructure when a user clicks "create."
2. Exposes the same gateway surface (Telegram, Discord, Slack, WhatsApp, etc.) as a self-hosted install, but managed.
3. Bills usage through [[engineering/hermes/2026-06-18-hermes-portal-teams|Teams billing]] — shared credit pool, per-member spend caps, Spend Intelligence dashboard.
4. Auto-discovers in Hermes Desktop: signing in with a Nous Portal account causes any active Cloud instances to appear in the Desktop sidebar without manual pairing.
5. Supports team provisioning — one operator creates agents for an entire organization from a single dashboard.

The instance itself is functionally equivalent to a self-hosted Hermes install running with the same version, the same skills catalog, the same plugin system, and the same gateway adapter set. The differences are operational, not architectural: where the binary lives, who pays the API bill, who manages the gateway token, and who handles restarts.

## The Provisioning Story

The 60-second claim decomposes into:

- **Pick a model** — choose from the Hermes model catalog (Claude Opus 5, Sonnet 4.5, GPT-5.6, Fable 5, DeepSeek V4 Flash 0731, Hy3, the MoA virtual models, etc.).
- **Pick a server size** — the launch announcement does not enumerate exact tiers but the team provisioning story implies more than one; the per-instance model picker ([[engineering/hermes/2026-08-02-hermes-v0-19-quicksilver-release#provider-updates|v0.19 Provider Updates]]) is the underlying UI primitive.
- **Click twice** — first to confirm the model/server choice, second to confirm billing against the Portal credit pool.
- **60 seconds later** — the instance is alive, has its own gateway token, and is reachable from any platform adapter or from Hermes Desktop.

The terseness is the feature. Self-hosted Hermes setup is roughly: install the binary, configure the provider, configure the gateway token, configure the platform adapter, expose the webhook endpoint, set up the systemd service, configure the cron tick, configure the secrets vault, configure the desktop connection. Cloud collapses all of that into the 60-second provisioning step. The trade is operational sovereignty for setup ergonomics.

## Desktop Discovers Cloud Instances

The single biggest UX change is **Hermes Desktop auto-discovers Cloud agents** (announcement Jul 10, 2026, 950+ likes). The flow:

1. User installs Hermes Desktop (native app).
2. User signs in with their Nous Portal account.
3. Active Cloud instances auto-appear in the Desktop sidebar.
4. Clicking one opens a session against that Cloud agent — same composer, same artifact viewer, same plugin surface, same voice mode.
5. Multiple Cloud instances can be open in parallel as multiple tabs.

This is the inverse of the pre-Cloud model. Before: Desktop had to be told about a Hermes instance via manual pairing (the SSH remote-backend connection mode in v0.20 is for self-hosted; the Desktop app's local pairing is for self-hosted gateway connections). After: signing in with Portal *is* the pairing.

The Cloud-side instance runs on Nous infrastructure; the Desktop-side is the front-end. The OpenAI data-residency endpoint routing work in v0.20 ([[engineering/hermes/2026-08-03-hermes-v0-20-herald-release]]) makes the routing sticky per-session, so a Cloud instance stays on the same backend for the duration of a session.

## rabbitOS Preinstall

A week after launch (Jul 10), Hermes Agent became preinstalled on **rabbitOS** — the operating system for the Rabbit r1 device. This is the first time Hermes shipped as part of a device OS. The preinstall means:

- A Rabbit device user gets Hermes out of the box without an install step.
- The device's input modalities (push-to-talk button, hardware keys) are wired into Hermes's voice/wake-word surface out of the box.
- Hermes's gateway adapter for the device (likely a Rabbit-specific adapter or a Photon/iMessage-derived surface) is preconfigured.
- Updates to Hermes ship through the rabbitOS update channel.

The significance is distribution, not just partnership. A preinstalled agent on a consumer device is qualitatively different from a downloadable CLI. The relationship between user and agent becomes ambient — the device is *the agent's host*, not a generic computer that happens to have an agent installed.

## Team Provisioning and Billing

The team provisioning story ties directly into [[engineering/hermes/2026-06-18-hermes-portal-teams|Teams billing]]:

- An organization admin creates a team in Nous Portal.
- Admin allocates a shared credit pool to the team.
- Admin sets per-member spend caps.
- Admin can spin up a Cloud Hermes instance per team member (or per team, or per project — the announcement does not pin the granularity, but the spend-cap model implies per-member).
- Spend Intelligence dashboard shows per-member and per-instance usage in real time.

This is the first time Hermes has had org-level provisioning baked in. The pattern matches what Notion, Linear, and Figma did in their enterprise tiers — the operator buys one license and provisions many seats — applied to an AI agent.

## What Cloud Means for Self-Hosters

Three concrete implications for operators running Hermes themselves:

1. **The portal account becomes the single sign-on surface.** Self-hosters who also have a Portal account now have one identity across both. The Desktop's auto-discovery is opt-in; if you do not sign in to Portal from Desktop, your local Hermes instance is unaffected.
2. **The model catalog converges.** Any model the Portal exposes is also available to a self-hosted Hermes via Portal's API. Self-hosters no longer need separate provider keys for OpenRouter/Anthropic if they have a Portal subscription — though credential-handling preferences (the [[engineering/hermes/2026-07-24-hermes-credential-firewall|credential firewall]] for Docker sandboxes, the Bitwarden/1Password vault providers) still apply.
3. **The gateway platform surface stays the same.** Telegram/Discord/Slack/WhatsApp tokens still belong to the operator; Cloud does not centralize them. The privacy model is "Portal knows which model you used and when; Portal does not see your Telegram chats."

## What Cloud Does Not Solve

A few things are explicitly *not* in Cloud's scope and worth naming so they are not misremembered:

- **Self-hosted Hermes is still the recommended path for sensitive data.** The Cloud-hosted instance routes through Nous infrastructure; for regulated workloads (healthcare, finance, anything with strict data-residency requirements), self-hosted with the data-residency endpoints from v0.20 is the path.
- **Cloud is not cheaper than BYO API keys at scale.** The Portal credit pool bundles the API cost with the convenience; high-volume users will pay more in aggregate than they would buying API keys directly. The win is operational, not financial.
- **Cloud is not multi-region yet.** The launch announcement does not name regions; operators who care about data residency for compliance reasons should still self-host with the OpenAI data-residency endpoints or AWS Bedrock (with the Converse API caching from v0.20).
- **Cloud is not an "agent marketplace."** There is no third-party agent catalog surfaced via Cloud at launch. The plugin system ([[engineering/hermes/plugins]] general plugins, [[engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin]] Desktop plugins) is the only extension surface so far.

## Operational Implications for This KB

Three follow-on effects for the rest of the Hermes cards in this KB:

1. **[[engineering/hermes/architecture]]** — the cloud-hosted instance is functionally a Hermes process running on Nous infrastructure. Architecture is unchanged; the operational layer changes.
2. **[[engineering/hermes/messaging]]** — the 22+ platform adapters are the same on Cloud. The operator configures platform tokens via Portal instead of via local `config.yaml`.
3. **[[engineering/hermes/profiles]]** — multi-profile isolation is preserved in Cloud. Each provisioned instance is its own profile home; profiles do not leak between Cloud users.

## Why This Launch Matters

The launch is the moment Hermes goes from "open-source project with a hosted option" to "product with an open-source core." The pattern is what GitLab did in 2018, what Supabase did in 2020, what Cursor did in 2023 — the OSS project stays open, but the easiest path for a new user is the hosted one. Nous Research has telegraphed this direction for over a year (the Portal launch, the Stripe partnership for billing, the Teams billing, the model catalog), but Cloud is the moment the hosted path *replaces* the install path as the default.

The bet: agent adoption is gated by setup friction, not by model quality. Quicksilver cut cold start 80% on a self-hosted install; Cloud collapses setup time to 60 seconds. The compounding is the story.

## Related

- [[engineering/hermes/2026-08-02-hermes-v0-19-quicksilver-release]] — preceding release; made the local install fast enough that hosted could compete on features instead of just convenience
- [[engineering/hermes/2026-08-03-hermes-v0-20-herald-release]] — same-day release; SSH remote-backend connection mode in Desktop is the bridge for self-hosted users who want the new Desktop surface without moving to Cloud
- [[engineering/hermes/messaging]] — gateway platform adapters; same surface on Cloud, different configuration path
- [[engineering/hermes/profiles]] — profile isolation preserved per Cloud instance
- [[engineering/hermes/architecture]] — internal architecture unchanged; Cloud is an operational layer
- [[engineering/hermes/2026-06-18-hermes-portal-teams]] — Teams billing, the billing substrate Cloud sits on top of
- [[engineering/hermes/README]] — overview of the Hermes system; Cloud is a new distribution channel alongside local install
