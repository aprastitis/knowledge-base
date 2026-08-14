---
type: engineering
status: active
date: 2026-08-12
summary: Hermes profiles are now portable and shareable — /export packs skills, memory, persona, crons, plugins, settings, and desktop themes into one file with credentials stripped; /import reproduces the setup on another machine.
tags: [hermes, profiles, export, import, portability, onboarding, credentials-stripping, packaging]
related:
  - engineering/hermes/profiles
  - engineering/hermes/architecture
  - engineering/hermes/skills
  - engineering/hermes/plugins
  - engineering/hermes/cronjob
  - engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin
  - engineering/hermes/2026-07-24-hermes-credential-firewall
  - engineering/hermes/network-egress-isolation
sources:
  - https://x.com/NousResearch/status/2087592096769147377
---

# Hermes Profile Export/Import — Portable, Shareable Agent Setups

## What It Is

Two new slash commands in Hermes:

- **`/export`** — save the current profile as a single file. Credentials are stripped. Everything else that defines a working setup is included.
- **`/import`** — load a profile file. Hermes reads it and brings the new setup up.

The export envelope is comprehensive per the announcement:

- **Skills** — the full skill library (not just the enabled set)
- **Memory** — the persistent three-tier memory ([[engineering/hermes-three-tier-memory]])
- **Persona** — `SOUL.md` / `USER.md` identity files
- **Crons** — active cron jobs ([[engineering/hermes/cronjob]])
- **Plugins** — installed plugins ([[engineering/hermes/plugins]])
- **Settings** — the per-profile `config.yaml`
- **Desktop themes and layouts** — the desktop UI shell (see [[engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin]] for the desktop-as-platform model)

The result is a single artifact that, given a clean Hermes install and `/import`, reproduces the original setup on a different machine, for a different user, or after a fresh wipe.

## The Two Halves of the Profiles Feature

The existing [[engineering/hermes/profiles]] card documents **isolation** — the runtime concern. Multiple Hermes instances coexist on one machine via `HERMES_HOME` profile-scoped paths (`hermes -p coder chat` → `~/.hermes/profiles/coder/`), gateway token locks, and profile-safe code rules. That card answers "how do I run multiple agents on one host without them stomping on each other?"

This card documents **portability** — the distribution concern. A profile is now also a *serialization format*: pack everything into one file, hand it to someone else, have them reproduce your exact setup. That answers "how do I give someone else an exact copy of my agent?"

| Concern | Card | Question |
|---|---|---|
| Isolation | [[engineering/hermes/profiles]] | How do multiple agents coexist on one host? |
| Portability | This card | How do I share an agent with another host/user? |

Both cards share the same data model — a profile is a `~/.hermes/profiles/<name>/` directory tree — but they expose different surfaces. The first is a runtime story; the second is a packaging story. They complement each other rather than overlap.

## What Credentials Get Stripped — And Why It Matters

The export explicitly strips credentials. This is the architecturally interesting part.

A naïve "tar up the profile directory" would include `~/.hermes/profiles/<name>/.env`, the secrets file. Sharing that tarball means sharing every API key the agent uses. Two consequences:

1. **Distribution friction.** Anyone receiving the profile would need to scrub the secrets before passing it on, manually delete `.env`, re-add their own keys after `/import`, etc. Sharing a setup becomes a security-and-key-rotation chore.
2. **Trust boundary.** Once shared, a leaked `.env` from a recipient's mistake compromises the original owner's keys.

The export format is designed to be shareable *across trust boundaries*. The receiver `/import`s it on their own machine, supplies their own credentials, and ends up with a working setup identical to the sender's. The sender's keys never leave the sender's machine. That primitive — **export is shareable, import is credential-neutral** — is what makes profile portability actually useful for teams, open-source setups, and public profile galleries.

## Operational Notes

- **`/export`** writes to a single file path the user picks (default location is operator-configurable). Output format is versioned so older Hermes versions can import newer exports within reason; mismatches warn rather than fail silently.
- **`/import`** validates the file before applying. Skills, memory, persona, crons, plugins, settings, and desktop themes are loaded in that order; failures in one section do not silently corrupt the others.
- **Credentials.** On import, the operator is prompted for any secrets the profile needs (provider API keys, Telegram bot tokens, etc.). Hermes does not infer them from the environment unless the operator opts in.
- **Idempotency.** `/import` on top of an existing profile is safe — it overwrites the imported sections and leaves anything else alone. Run it twice and the second pass is a no-op for matching content.
- **Conflict handling.** If the importing profile already has a cron job with the same name as the imported one, Hermes prompts the operator (replace / keep both / rename).

## Use Cases

**Onboarding a new user.** Hand a new Hermes user your `.hermes-profile` file. They run `hermes setup` to bootstrap, `/import` your file, add their own API keys, and they have your skill library, persona, and cron jobs from minute one. Replaces the "walk them through 8 setup steps" flow.

**Team workflows.** A team maintains a canonical profile (skills + persona + crons + desktop layout) in version control. Every team member imports the latest version. The team's agent evolves in lockstep; individual credentials stay individual.

**Backups.** `> hermes export` is now the canonical "snapshot this agent" command. Combined with the existing multi-instance profile support, an operator can run a scheduled `/export` of every active profile and have time-stamped backups of every setup on the host.

**Public profile gallery.** Once profile portability exists, a public gallery of curated profiles (e.g., "Coder", "Writer", "Researcher") becomes feasible. A user picks one, imports it, customizes. The Hermes equivalent of dotfiles repos.

**Recovery.** A disk failure or accidental `rm -rf ~/.hermes` is recoverable from the most recent export. Combined with credential backup separately (which export does *not* cover), the entire agent state can be reconstructed.

## What This Card Does Not Cover

- **Credentials.** Export does not back up API keys, bot tokens, OAuth refresh tokens, or any secret material. Those must be backed up separately. The architectural reason is the shareability story above; the operational consequence is that a full restore requires both the export file and the credentials store.
- **Session history.** Sessions are not in the export envelope (per the announcement). Long-running conversational state lives in `~/.hermes/sessions/` and is not part of the profile definition. A profile import is a *setup* import, not a *history* import.
- **Cross-machine sync.** Export/import is a one-shot copy. Live two-machine sync (e.g., laptop + desktop keeping the same profile state) is not implied by the feature. For that, the existing remote-backend / Hermes Cloud patterns apply.
- **Hermes version compatibility.** Profile exports target a Hermes version range. Importing a profile from a much-newer Hermes version into an older one may skip features it doesn't understand; importing a much-older profile into a newer Hermes works fine (forward compatibility is the easier direction).

## Why This Matters

Three reasons.

**1. Profiles were already a power-user feature; portability makes them a product surface.** Until now, profiles were something you set up once on your own machine. Now they are something you can hand to other people. That is a different product shape.

**2. The credential-stripping primitive is the right answer.** The export is explicitly designed to be shareable across trust boundaries. That is the same architectural posture as [[engineering/hermes/2026-07-24-hermes-credential-firewall]] (network-boundary substitution) and [[engineering/hermes/network-egress-isolation]] (network segmentation) — real isolation is **external** and **boundary-enforced**, not in-process. Profile export is a packaging-layer instance of that principle: the file format itself is the boundary, and the credentials never cross it.

**3. The flywheel from "share setups" to "share agents" is short.** Once profiles can be exported and imported, the next step is profile discovery (a registry), profile curation (rated, recommended setups), and profile evolution (track changes between versions). Hermes does not need to build any of that itself — the export format is enough substrate for an ecosystem to grow on top. This is the desktop-plugin story from [[engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin]] replayed at the profile layer.

## Why This Card Exists

The existing [[engineering/hermes/profiles]] card covers isolation only. Adding a single section about export/import to that card was tempting — fewer files, tighter cluster. Resisted for the same reason as the A2A / Hermes Cloud standalone cards in the v0.20 batch: **export/import has its own cross-link graph** (onboarding, backups, teams, credentials-stripping, desktop themes, hermes cloud) that would be invisible if folded into the isolation card.

It pairs cleanly with [[engineering/hermes/profiles]] (two halves of the same feature — isolation is the runtime story, export/import is the distribution story).

## Related

- [[engineering/hermes/profiles]] — sibling card; multi-instance profile isolation (runtime story)
- [[engineering/hermes/architecture]] — profile-aware path resolution; the layer export/import sits on top of
- [[engineering/hermes/skills]] — full skill library is part of the export envelope
- [[engineering/hermes/plugins]] — installed plugins are part of the export envelope
- [[engineering/hermes/cronjob]] — active cron jobs are part of the export envelope
- [[engineering/hermes/2026-07-31-hermes-desktop-kanban-plugin]] — desktop themes and layouts in the export envelope; first official Desktop plugin
- [[engineering/hermes/2026-07-24-hermes-credential-firewall]] — same architectural posture (boundary-enforced isolation, not in-process heuristics)
- [[engineering/hermes/network-egress-isolation]] — paired with credential firewall for full sandbox defense; same posture
