---
type: engineering
status: active
date: 2026-08-16
summary: Hermes Agent exposes a zero-data-retention opt-out at the inference-provider layer — a privacy primitive on the cloud-side answer to "where do my inferences go?", distinct from OS-level isolation. Pairs with the local-inference story ([[engineering/hermes/2026-08-06-hermes-actual-computer-local-inference]]).
tags: [hermes, security, privacy, provider, data-retention, opt-out, inference, compliance]
related:
  - engineering/hermes/security
  - engineering/hermes/2026-08-06-hermes-actual-computer-local-inference
  - engineering/hermes/network-egress-isolation
  - engineering/hermes/2026-07-24-hermes-credential-firewall
  - engineering/hermes/plugins
  - engineering/hermes/architecture
sources:
  - https://x.com/NousResearch/status/2088962906356380119
---

# Hermes Agent — Zero-Data-Retention Opt-Out (Provider Layer)

On Sun Aug 16 2026 at 12:15 UTC, @NousResearch confirmed in a reply that Hermes Agent **provides a zero data retention opt-out** at the inference-provider layer.

> **Nous Research:** "@SirShmevin @Suhail We provide a zero data retention opt out"

Engagement on the source tweet is low (2 likes, 0 reposts, 0 replies) — a defensive reply to a question about inference privacy. The substantive claim is unambiguous: there is an opt-out that routes the agent's inferences through a provider pipeline that does not retain logs.

## What This Is

A **privacy primitive on the provider path**. For users with strict confidentiality requirements — HIPAA, GDPR strict mode, NDAs, personal health, regulated industry work — the question is not just "can an adversarial LLM reach my filesystem" (covered by [[engineering/hermes/security]]'s OS-level isolation) but also "is what I send to the inference API kept on someone else's storage" (covered by retention policy). The opt-out addresses the second question at the **inference provider boundary** — a third boundary distinct from the OS boundary and the subprocess / network boundaries already documented.

This is **not** in [[engineering/hermes/security]]. That card covers the trust model (OS-level isolation as the only real boundary), in-process heuristics, plugin trust, credential scoping, and external surfaces — but it does not document a provider-level "zero retention" mode. The card here fills that gap.

## What This Is Not

- **Not a security boundary.** Per the [[engineering/hermes/security]] posture, "nothing inside the agent process constitutes containment." A provider-level retention policy is a **policy commitment by the provider**, not a technical boundary. If the provider's policy is broken or the configuration is wrong, the retention promise is not enforced by Hermes.
- **Not the same as local inference.** Local inference ([[engineering/hermes/2026-08-06-hermes-actual-computer-local-inference]]) is the strongest answer to the privacy question — your prompts and completions never leave your hardware. Zero-data-retention opt-out is the **cloud-provider** answer — your prompts and completions leave your hardware, hit the provider's API, but are configured to not be retained on the provider's storage. Same privacy concern, two different primitive answers. They are complementary, not competing.
- **Not a Hermes-controlled guarantee.** The opt-out is a configuration hook that points Hermes's inference traffic at a provider path with a "no retention" policy. The actual non-retention is the provider's responsibility. Hermes's role is to surface the configuration and route the traffic; the provider's role is to honor the policy.
- **Not a replacement for credential hygiene.** The [[engineering/hermes/2026-07-24-hermes-credential-firewall]] and [[engineering/hermes/network-egress-isolation]] primitives prevent credentials from being exfiltrated on the network. Zero-data-retention prevents prompts/completions from being retained on someone else's storage. Both layers are needed; neither replaces the other.

## What It Implies About the Provider Layer

For there to be an opt-out, there has to be **at least one path that does retain by default**, plus the configuration hook to switch. This implies:

- The **inference-provider layer is configurable** in Hermes — at minimum at the level of "which provider path is this inference routed through." The model-providers plugin surface in [[engineering/hermes/plugins]] is the natural home for this switch; a zero-retention provider entry alongside the standard retention-by-default entries.
- The opt-out is **per-provider**, not global. A user can keep their default provider for routine work and route only the sensitive sessions through the zero-retention path. The natural shape is "add a provider entry with `data_retention: zero` (or equivalent config key) and select it for the session / profile that needs it."
- The opt-out is **not free**. Providers that offer true zero-data-retention typically charge a premium or have a different SLA — no-retention costs the provider more (they cannot use the traffic for evals, RLHF, or model improvement). The card cannot quantify the cost without primary-source provider docs; the cost will vary by provider and plan.
- The opt-out is **subject to provider policy changes**. The user's configuration is honored as long as the provider honors its own policy. If the provider retires the zero-retention tier or changes its retention policy, the user's protection degrades with it. Watch the provider changelog for retention-tier changes.

## Why This Card Exists

**1. It is a privacy primitive on a boundary the KB did not yet document.** [[engineering/hermes/security]] enumerates the trust-model layers but did not include the provider-retention layer. The card names the layer, places it on the existing trust-model diagram (third boundary: inference provider), and makes the option retrievable.

**2. It pairs with [[engineering/hermes/2026-08-06-hermes-actual-computer-local-inference]].** That card covers the local-inference answer to "where do my inferences go?" This card covers the cloud-provider answer. They are sibling cards: same privacy question, two different primitive answers. Both cards should be retrieved together when answering a "is Hermes safe for [sensitive workload]?" question.

**3. It enables a defensive answer to compliance questions.** "Hermes is HIPAA-friendly" or "Hermes is GDPR-strict-mode-compatible" requires being able to point at a configuration that demonstrably prevents inference content from being retained. Before this card, the only answer was "run Hermes locally with Actual Computer." After: "either run Hermes locally with Actual Computer, or configure your provider for zero-data-retention."

## Open Questions (Not Yet Confirmed by Primary Source)

The tweet is a 14-word reply with no follow-up architecture post. The card flags the following as unconfirmed:

- **How is the opt-out configured?** — provider flag? model catalog entry? dedicated config key? Most likely the model-providers plugin surface ([[engineering/hermes/plugins]]) but not confirmed.
- **Which providers support it?** — Nous Portal's own provider? OpenRouter? Direct provider integrations (Anthropic, OpenAI, etc.)? Each provider has its own retention policy and not all offer a zero-retention tier.
- **What does "zero data retention" actually mean for each provider?** — does it include prompt content? completion content? embeddings? metadata (timestamps, model used, token counts)? Provider definitions vary; "zero retention" is not a single standardized contract.
- **What is the cost?** — likely higher than default-retention; the magnitude is provider-specific.
- **Does the opt-out propagate to subagents and async delegation?** — i.e., if the parent agent has zero-retention configured, do the subagents spawned via `delegate_task` ([[engineering/hermes/architecture]]) inherit the same configuration? Default behavior is unknown.

These questions will be answered as primary-source documentation (provider config docs, Hermes changelog, community answers) surfaces. The card will be expanded with confirmed answers when they land.

## Source Notes

- Tweet is a 14-word defensive reply to a privacy question. No primary-source architecture post, no provider config docs, no SDK reference in the tweet. The card is intentionally **thin on confirmed specifics** and explicit about what is inferred vs what is claimed.
- "We provide a zero data retention opt out" is Nous's claim, not a measured privacy audit. The strength of the claim depends on which providers implement the opt-out, how it is configured, and what the provider's own retention policy actually says.

## Related

- [[engineering/hermes/security]] — the trust model and the OS-level isolation as the only real boundary. This card extends the trust model with a provider-layer primitive.
- [[engineering/hermes/2026-08-06-hermes-actual-computer-local-inference]] — the local-inference sibling card. Same privacy question, different primitive answer.
- [[engineering/hermes/network-egress-isolation]] — Docker network segmentation primitive; pairs with zero-data-retention to cover both "where can the agent reach" and "what does the provider retain."
- [[engineering/hermes/2026-07-24-hermes-credential-firewall]] — token-substituting proxy at the Docker sandbox boundary; another defense-in-depth primitive on the network path.
- [[engineering/hermes/plugins]] — the model-providers plugin surface; likely where the zero-retention provider entry is registered.
- [[engineering/hermes/architecture]] — the agent loop, delegation, and profile system. Open question: does the zero-retention configuration propagate to subagents?
