---
type: card
status: active
date: 2026-06-12
summary: Multi-instance isolated profiles — profile-aware paths, gateway token locks, profile-safe code rules
tags: [hermes, profiles, multi-instance, isolation]
sources:
  - https://github.com/NousResearch/hermes-agent/blob/main/AGENTS.md
related:
  - engineering/hermes/architecture
  - engineering/hermes/kanban
  - engineering/hermes/2026-08-12-portable-profiles-export-import
---

# Hermes Profiles — Multi-Instance Support

Hermes supports **profiles** — multiple fully isolated instances, each with its own `HERMES_HOME` directory (config, API keys, memory, sessions, skills, gateway, etc.).

## Core Mechanism

`_apply_profile_override()` in `hermes_cli/main.py` sets `HERMES_HOME` before any module imports. All `get_hermes_home()` references automatically scope to the active profile.

```
hermes -p coder chat          # Uses ~/.hermes/profiles/coder/
hermes -p writer kanban ls    # Uses ~/.hermes/profiles/writer/
hermes profile list           # Lists all profiles
```

## Profile-Safe Code Rules

1. **Use `get_hermes_home()` for all HERMES_HOME paths.** NEVER hardcode `~/.hermes` or `Path.home() / ".hermes"`

   ```python
   # GOOD
   from hermes_constants import get_hermes_home
   config_path = get_hermes_home() / "config.yaml"

   # BAD — breaks profiles
   config_path = Path.home() / ".hermes" / "config.yaml"
   ```

2. **Use `display_hermes_home()` for user-facing messages.** This returns `~/.hermes` for default or `~/.hermes/profiles/<name>` for profiles.

3. **Module-level constants are fine** — they cache `get_hermes_home()` at import time (after `_apply_profile_override()` sets the env var).

4. **Tests that mock `Path.home()` must also set `HERMES_HOME`:**

   ```python
   with patch.object(Path, "home", return_value=tmp_path), \
        patch.dict(os.environ, {"HERMES_HOME": str(tmp_path / ".hermes")}):
       ...
   ```

5. **Gateway platform adapters should use token locks** — call `acquire_scoped_lock()` from `gateway.status` in `connect()`/`start()` and `release_scoped_lock()` in `disconnect()`/`stop()`. This prevents two profiles from using the same credential simultaneously.

6. **Profile operations are HOME-anchored, not HERMES_HOME-anchored** — `_get_profiles_root()` returns `Path.home() / ".hermes" / "profiles"`, NOT `get_hermes_home() / "profiles"`. This lets `hermes -p coder profile list` see all profiles regardless of which one is active.

## Multi-Gateway Deployment

Hermes supports multiple gateway processes running concurrently — one per profile. Only one gateway owns the kanban dispatcher (`dispatch_in_gateway: true`); all others set it to `false`.

**Why this matters:** A gateway with `dispatch_in_gateway: true` opens per-board SQLite connections for both the dispatcher and the notifier watcher. Multiple gateways doing this concurrently multiplies open file descriptors on `kanban.db` and amplifies WAL `-shm` reader contention.

| Gateway role | dispatch_in_gateway | Opens per-board DBs? | Runs dispatcher + notifier? |
|---|---|---|---|
| default (dispatch owner) | true (default) | yes | yes |
| writer, admin, coder, etc. | false | no | no |

**Configuration** (on non-dispatch gateways):
```yaml
kanban:
  dispatch_in_gateway: false
```

Or: `HERMES_KANBAN_DISPATCH_IN_GATEWAY=false`

## Profile Builder (Dashboard)

A dashboard-native profile creation experience is being designed. A profile = a full `~/.hermes/profiles/<name>/` directory with:
- `config.yaml` — holds `model`/`provider`, `mcp_servers`, enabled skills
- `skills/` — physical SKILL.md files (built-in seed + optional + hub installs)
- `.env` — secrets
- `SOUL.md` / `USER.md` — identity

Per-profile scoping of Model, MCPs, and Skills is **native** — no data-model change needed. The gap is purely UX.

## Sibling: Distribution via Export/Import

The two halves of the profiles feature:

- **This card — isolation.** How do multiple agents coexist on one host without stomping on each other?
- **[[engineering/hermes/2026-08-12-portable-profiles-export-import]] — portability.** How do I share an agent with another host/user? `/export` and `/import` make profiles portable and shareable; the export envelope explicitly strips credentials so the file is safe to hand across trust boundaries.

Both cards share the same data model (`~/.hermes/profiles/<name>/`) but expose different surfaces — isolation is a runtime story, portability is a distribution story.