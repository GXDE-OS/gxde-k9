# AGENTS.md

Guidance for AI agents working on the gxde-k9 codebase.

## Project overview

gxde-k9 is a lightweight, user-space watchdog / service manager for GXDE and
other Linux environments where systemd is unavailable (containers, proot,
Termux, etc.). It is written entirely in bash and has **no external runtime
dependencies** beyond `bash`, `coreutils`, and a TUI helper
(`whiptail` or `dialog`).

The project ships two executables:

| Binary             | Role                                                    |
|--------------------|---------------------------------------------------------|
| `gxde-k9`          | The daemon. Monitors directories and executes tasks.   |
| `gxde-k9-chocker`  | The "collar" management CLI/TUI for tasks and logs.    |

### Task categories

Tasks are plain files placed in category directories. The file extension
controls whether the task is active:

| Extension                | Meaning          |
|--------------------------|------------------|
| `.<category>`            | enabled (active) |
| `.<category>.disabled`   | disabled         |

| Category | Behavior                                              |
|----------|-------------------------------------------------------|
| `slimy`  | Cycle-executed every 5 seconds.                       |
| `timer`  | crontab-style schedule (5-field cron + `\|` + cmd).   |
| `shot`   | Executed once at daemon start.                        |
| `edging` | Keep-alive: launches and supervises a target process. |

### Scope (user vs system)

- **user** — `~/.local/share/GXDE/gxde-k9/<category>/` (default, no privilege)
- **system** — `/usr/share/gxde-k9/<category>/` (installed system-wide)

K9 is a **user-space service manager**. Services are normally started by the
user's own daemon instance, not by root. Consequently:

- `start` / `stop` / `restart` — runtime process control, **no privilege**
  needed (they only read config and manage processes).
- `enable` / `disable` / `remove` — modify config files; for **system** scope
  these require privilege escalation (`pkexec` / `sudo`).
- `add` / `import` — create configurations; the **TUI only creates user-scope**
  configs. The CLI `--system` flag is retained for packagers / root use.

## Repository layout

```
src/
  usr/bin/
    gxde-k9              # daemon (bash)
    gxde-k9-chocker      # management tool (bash)
  usr/share/gxde-k9/      # default system base dir (example tasks)
    slimy/ timer/ shot/ edging/
  usr/share/bash-completion/completions/
    gxde-k9
    gxde-k9-chocker
  etc/xdg/autostart/
    gxde-k9.desktop
debian/
  control  rules  changelog  install  ...
```

Both main scripts are single-file bash programs. There is no build step; files
under `src/` are installed verbatim.

## Development guidelines

### Bash style

- Target `bash` 4+. Avoid `sh`-only constructs.
- Use `[[ ]]` for tests, `local` for function variables.
- Keep functions small and focused; group by feature with banner comments.
- Preserve the logging helpers (`log.info`, `log.warn`, `log.error`, `log.ok`).
- Do **not** introduce external dependencies (no `jq`, `python`, etc.) without
  strong justification — keeping zero non-core deps is a project invariant.

### TUI (chocker)

- The TUI backend is abstracted via `_tui_run`; `whiptail` is preferred,
  `dialog` is the fallback. Test changes against both if possible.
- `tui_menu` supports a default highlighted item through the `_TUI_DEFAULT_ITEM`
  global variable (consumed and cleared on each call). Use this to remember the
  user's last selection when returning to a menu loop.
- When adding new menu loops, remember the previous selection so navigation
  feels continuous across sub-menus.

### Service management semantics

- `enable` / `disable` only rename the file (add/remove `.disabled`). `disable`
  also stops associated processes first.
- `start` / `restart` require the task to be enabled; `stop` does not change
  the enabled state (the daemon may relaunch `slimy`/`edging` — use `disable`
  to prevent that).
- `timer` tasks are scanned by the daemon on schedule; they only support
  `enable` / `disable`, not start/stop.

### Privilege escalation

- Only `enable`, `disable`, and `remove` on **system** scope escalate.
  `elevate_task_action` handles the confirm + re-exec flow.
- `add` / `import` from the TUI are user-scope only. The CLI keeps `--system`
  for direct root / packager use.
- Do not add privilege escalation to read-only or runtime operations.

## Testing

There is no automated test suite. To manually validate:

```bash
bash -n src/usr/bin/gxde-k9          # syntax check daemon
bash -n src/usr/bin/gxde-k9-chocker  # syntax check chocker
./src/usr/bin/gxde-k9-chocker list   # smoke test (should print task table)
```

When changing TUI flows, run `./src/usr/bin/gxde-k9-chocker` interactively
inside a terminal that has `whiptail` or `dialog` installed.

## Versioning

- Versions are tracked in three places, keep them in sync on every release:
  1. `debian/changelog` (Debian revision, e.g. `2.0.0`)
  2. `VERSION=` inside `src/usr/bin/gxde-k9`
  3. `VERSION=` inside `src/usr/bin/gxde-k9-chocker`
- Use semantic versioning: bump major for breaking behavior changes, minor for
  features, patch for fixes.

## Commit and release

- Commit messages should be concise; follow the style already in `git log`.
- Releases are tagged as `v<version>` (e.g. `v2.0.0`).
