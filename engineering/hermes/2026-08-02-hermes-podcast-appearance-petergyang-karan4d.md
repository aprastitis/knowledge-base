---
type: engineering
status: active
date: 2026-08-02
summary: Hermes team appeared on the @petergyang / @karan4d show on 2026-08-02 — captured as a thin public-presence marker. No substantive content beyond the "thank you for having us" tweet; primary value is the appearance itself + the date.
tags: [hermes, public-presence, podcast, nousresearch]
sources:
  - https://x.com/NousResearch/status/2083950025386660043
related:
  - engineering/hermes/README
---

# Hermes — Peter Yang / Karan 4D Show Appearance (2026-08-02)

Thin public-presence marker. On 2026-08-02 the NousResearch account posted `@petergyang @karan4d Great conversation, thank you for having us!` (32 likes, 0 reposts, 4 replies) — confirming a Hermes-team appearance on the Peter Yang / Karan 4D show on or shortly before that date.

The tweet itself carries no substantive technical content. The only information recoverable from the post is the fact of the appearance and the date. There is no linked episode URL, no quoted clip, no specific Hermes topic called out, and no follow-up Nous tweet elaborating.

## What We Know

- **Date of tweet:** 2026-08-02, 16:16 UTC
- **Co-guests/hosts mentioned:** Peter Yang (@petergyang) and Karan 4D (@karan4d)
- **Speaker:** NousResearch account (i.e., a Hermes-team member, not the @hermes_agent account)
- **Engagement:** low (32 likes) — consistent with a "thank you" tweet posted after-the-fact rather than an announcement of the appearance itself

## What We Don't Know

- Episode title / topic
- Hermes-side speaker(s) — likely a Teknium appearance given the timing and account, but not confirmed by the tweet
- Whether the conversation covered v0.18 "Judgment" (released ~6 weeks earlier) and/or v0.19 "Quicksilver" (released ~2 weeks earlier) and/or the Desktop plugin system (announced 2 days prior)
- Whether the show is video, audio, or both
- Where the episode is hosted (YouTube, Spotify, etc.)

## Why This Card Exists Anyway

The card exists to anchor the appearance in the KB so future reference ("did Hermes appear on any podcasts in 2026?") has a hit. Without it, the public-presence track is invisible. The 32-like tweet would normally not justify its own card, but:

- It confirms a real public appearance (not speculation)
- It is Hermes-adjacent in the broad sense
- The KB currently has no other podcast/interview anchors for Hermes, so this is a category-of-one entry

If a richer source surfaces later (full episode link, transcript, specific Hermes topics covered), this card should be **upgraded** with that content and re-tagged accordingly. If nothing materializes within ~12 weeks, consider moving to `.archive/` with a note that the appearance was real but undocumented beyond the thank-you tweet.

## What Was Considered

- **Skip the card entirely** — rejected; the scan rule says "not in the KB" items should be added when they have a source, even thin ones. Anchoring public presence has cumulative value.
- **Queue instead of ingest** — rejected; queue is for material too long to process in one session, not material too thin to deserve a card. A thin card with explicit "what we don't know" is a legitimate KB primitive.
- **Place in `people/`** — considered (a `people/petergyang-karan4d.md` or similar). Rejected: we do not have enough about the hosts themselves yet, and the show is the relevant unit, not the individuals. If they appear as recurring Hermes interlocutors, the hosts may later warrant their own `people/` cards.

## Related

- [[engineering/hermes/README]] — Hermes Agent entry point; this card lives in the same cluster so the public-presence marker is co-located with the framework documentation.
