---
type: card
status: active
date: 2026-06-12
summary: Dev setup, contribution priorities, skill vs tool decision, cross-platform rules, dependency pinning, PR process
tags: [hermes, contributing, development, setup]
sources:
  - https://github.com/NousResearch/hermes-agent/blob/main/CONTRIBUTING.md
related:
  - engineering/hermes/README
  - engineering/hermes/architecture
  - engineering/hermes/security
---

# Contributing to Hermes Agent

## Contribution Priorities

1. **Bug fixes** — crashes, incorrect behavior, data loss
2. **Cross-platform compatibility** — macOS, Linux, WSL2
3. **Security hardening** — shell injection, prompt injection, path traversal
4. **Performance and robustness** — retry logic, error handling
5. **New skills** — broadly useful ones only
6. **New tools** — rarely needed (most should be skills)
7. **Documentation** — fixes, clarifications, examples

## Development Setup

```bash
git clone https://github.com/NousResearch/hermes-agent.git
cd hermes-agent

uv venv venv --python 3.11
export VIRTUAL_ENV="$(pwd)/venv"
uv pip install -e ".[all,dev]"

mkdir -p ~/.hermes/{cron,sessions,logs,memories,skills}
cp cli-config.yaml.example ~/.hermes/config.yaml
touch ~/.hermes/.env
echo "OPENROUTER_API_KEY=***" >> ~/.hermes/.env

mkdir -p ~/.local/bin
ln -sf "$(pwd)/venv/bin/hermes" ~/.local/bin/hermes

hermes doctor
hermes chat -q "Hello"
```

Run tests: `scripts/run_tests.sh` (preferred — matches CI)

## Should It Be a Skill or a Tool?

**Skill when:**
- Capability expressed as instructions + shell commands + existing tools
- Wraps external CLI or API the agent can call via `terminal` or `web_extract`
- Doesn't need custom Python integration or API key management

**Tool when:**
- Requires end-to-end integration with API keys, auth flows, multi-component configuration
- Needs custom processing logic that must execute precisely every time
- Handles binary data, streaming, or real-time events that can't go through terminal

## Memory Providers: Ship as Standalone Plugin

**No new in-tree memory providers.** The set under `plugins/memory/` (honcho, mem0, supermemory, byterover, hindsight, holographic, openviking, retaindb) is closed.

Publish new memory backends as standalone plugin repos installed into `~/.hermes/plugins/`. They implement the same `MemoryProvider` ABC and integrate via `hermes memory setup`.

## Cross-Platform Compatibility

> Run `scripts/check-windows-footguns.py` to catch common Windows-unsafe patterns before PR.

**Critical rules:**

1. **Never call `os.kill(pid, 0)` for liveness checks.** On Windows it broadcasts `CTRL_C_EVENT` to the **entire console process group** — "probe if alive" silently becomes "kill the target and often unrelated processes sharing its console." Use `psutil.pid_exists(pid)`.

2. **Use `shutil.which()` before shelling out** — don't assume Windows has Linux tools (`ps`, `kill`, `grep`, `awk`, `fuser`, `lsof`, `pgrep`)

3. **`termios` and `fcntl` are Unix-only** — catch both `ImportError` and `NotImplementedError`

4. **File encoding** — Windows may save `.env` files in `cp1252`. Handle: `load_dotenv(env_path, encoding="latin-1")` as fallback

5. **Process management** — `os.setsid()`, `os.killpg()`, `os.fork()` differ on Windows. Use `psutil` for cross-platform child killing.

6. **Signals that don't exist on Windows:** `SIGALRM`, `SIGCHLD`, `SIGHUP`, `SIGUSR1`, `SIGUSR2`, `SIGPIPE`, `SIGQUIT`, `SIGKILL`

7. **Path separators** — use `pathlib.Path` instead of string concatenation with `/`

8. **Symlinks need elevated privileges on Windows** — skip tests with `@pytest.mark.skipif(sys.platform == "win32", ...)`

9. **POSIX file modes (0o600, 0o644) NOT enforced on NTFS** — skip mode assertions on Windows

10. **Detached background daemons on Windows need `pythonw.exe`** — combine with `CREATE_NO_WINDOW | DETACHED_PROCESS | CREATE_NEW_PROCESS_GROUP | CREATE_BREAKAWAY_FROM_JOB`

11. **`subprocess.Popen` with `.cmd`/`.bat` shims needs `shutil.which`** to resolve (PATHEXT honors `.CMD` on Windows)

12. **Don't use shell shebangs to run Python** — `#!/usr/bin/env python` fails on Windows. Always invoke explicitly: `[sys.executable, "myscript.py"]`

## Dependency Pinning Policy

After litellm compromise (March 2026) and Mini Shai-Hulud worm (May 2026):

| Source type | Treatment |
|-------------|-----------|
| PyPI package | `>=floor,<next_major` |
| Git URL | Full commit SHA |
| GitHub Actions | Full commit SHA + version comment |
| CI-only pip | `==exact` |

**How to determine the ceiling:**
- Post-1.0: `<next_major` (e.g. `>=1.5.0,<2`)
- Pre-1.0: `<0.(current_minor + 2)` (e.g. `>=0.29,<0.32`)

## Pull Request Process

**Branch naming:** `fix/description`, `feat/description`, `docs/description`, `test/description`, `refactor/description`

**Commit messages:** [Conventional Commits](https://www.conventionalcommits.org/):
```
<type>(<scope>): <description>
```
Types: `fix`, `feat`, `docs`, `test`, `refactor`, `chore`
Scopes: `cli`, `gateway`, `tools`, `skills`, `agent`, `install`, `whatsapp`, `security`, etc.

**Before submitting:**
1. Run tests: `scripts/run_tests.sh`
2. Test manually — exercise the code path you changed
3. Check cross-platform impact
4. Keep PRs focused — one logical change per PR