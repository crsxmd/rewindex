---
name: rewindex
version: 1.0.2
description: |
  Rewindex is a file snapshot tool that runs as a background daemon. Use this skill when:
  - The user asks to view, rewind, or manage file snapshots
  - You need to undo AI-generated changes to specific files
  - You want to check what changed recently in the project
  - You need to annotate snapshots with context about your changes
  Rewindex auto-snapshots files when they change — you do NOT need to trigger snapshots manually.
  Files are snapshotted immediately on save (no debounce delay).
allowed-tools: Bash
---

# Rewindex CLI — Agent Reference

Rewindex watches project files and creates snapshots automatically on every file save. You interact with it through CLI commands.

## Setup

```bash
pip install rewindex
rewindex init --project "[name]" --folder [/path]
```

Do **not** run `rewindex init` without flags — it hangs waiting for input. Non-interactive mode starts the daemon and configures autostart automatically.

Only one daemon runs system-wide. If `rewindex start` reports `daemon is already running`, do not retry.

## IDs

All IDs use typed prefixes with **no zero-padding**:

| Type | Format | Example |
|------|--------|---------|
| Session | `SS` + integer | `SS3`, `SS12` |
| Snap | `SN` + integer | `SN42`, `SN7` |
| FileID | 4-char alphanumeric | `D1VS`, `U5XD` |
| File version | `v` + integer | `v1`, `v3` |
| Project | `PJ` + integer | `PJ1`, `PJ2` |

`latest` is accepted anywhere a version or session ID is expected.

## Project Selection

Commands auto-detect the project from CWD. Use `-p` to override:

```bash
rewindex log -p PJ1
rewindex snap -p my-app
```

## Commands

### `log` — View history

```bash
rewindex log                     # session list
rewindex log sn                  # flat list of all snapshots
rewindex log --detail            # session list with all versions per session
rewindex log --full              # session list with full project state
rewindex log --limit 20          # limit results
rewindex log --reverse           # oldest first
rewindex log --since 18/04/26    # filter by date
rewindex log --folder src/       # filter by path prefix
rewindex log --from SN5 --to SN20   # range by snap ID
rewindex log --from SS2 --to SS8    # range by session ID
rewindex log D1VS                # file version history
rewindex log SS12                # full project state at a session
```

### `diff` — View what changed

```bash
rewindex diff D1VS             # previous → latest version
rewindex diff D1VS v5          # v5 → latest
rewindex diff D1VS v3 v5       # v3 → v5
rewindex diff D1VS v3 latest   # v3 → latest
```

### `snap` — Force a checkpoint

Snapshots happen automatically on save. Use `snap` when you want to force a checkpoint and annotate it.

```bash
rewindex snap                    # snap all changed files
rewindex snap "before refactor"  # snap with a session note
```

Does nothing if no files changed.

### `rewind` — Restore files

```bash
rewindex rewind SS3 --yes            # full project-state restore at session
rewindex rewind D1VS v4 --yes        # restore one file to a specific version
rewindex rewind D1VS latest --yes    # restore one file to latest version
rewindex rewind SN -1 --yes          # undo last 1 snapshot (all affected files)
rewindex rewind SN -5 --yes          # undo last 5 snapshots (all affected files)
```

**Always use `--yes`** — without it, Rewindex waits for confirmation and agents hang.

**Semantics:**
- `rewind SS3` = all files restored to their state at that session
- `rewind D1VS v4` = one file to a specific version
- `rewind SN -N` = undo last N snapshots across **all** files touched by those snaps

### `note` — Annotate

```bash
rewindex note D1VS v3 "fix: null check"    # annotate a file version
rewindex note D1VS "refactor done"          # defaults to latest version
rewindex note SS3 "before deploy"           # annotate a session
rewindex note SS latest "checkpoint"        # annotate latest session
rewindex note SN42 "daemon auto-snap"       # annotate a snap
```

Running `note` on a target that already has a note replaces it.

### `status` — Check daemon and projects

```bash
rewindex status
```

### `project` — Manage projects

```bash
rewindex project                                              # list all
rewindex project add MyApp ~/projects/my-app                  # add (positional)
rewindex project add --name MyApp --folder ~/projects/my-app  # add (flags)
rewindex project remove my-app --yes                          # remove
rewindex project rename old-name new-name --yes               # rename
rewindex project add-folder my-app /path/to/folder            # add folder
rewindex project remove-folder my-app /path/to/folder --yes   # remove folder
rewindex project folder add my-app /path/to/folder            # add folder (alt syntax)
rewindex project folder remove my-app /path/to/folder --yes   # remove folder (alt syntax)
rewindex project exclude add "*.log"                          # global exclude
rewindex project exclude add "*.log" -p my-app                # per-project exclude
```

### `member` — Plan and license

```bash
rewindex member                    # show current plan
rewindex member --license <key>    # activate a Pro license key
```

### `setting` — Session limits

```bash
rewindex setting                          # show current settings
rewindex setting --safe <n>               # sessions kept safe from trim
rewindex setting --max <n>                # trim triggers at this count
rewindex setting precompute on|off        # toggle pre-compute fullbase (Pro)
```

### `cleanup` — Manage trash

```bash
rewindex cleanup --trash                         # show trashed projects
rewindex cleanup --trash --yes                   # permanently delete all
rewindex cleanup --trash restore <name|PJID>     # restore a trashed project
```

### `update` — Update Rewindex

```bash
rewindex update            # show version, check PyPI
rewindex update --latest   # upgrade to latest
rewindex update --sync     # sync config defaults
```

## Workflows

### After finishing a task

Always snap and annotate when you finish work:

```bash
rewindex snap "feat: add rate limiting to /api/upload"
```

Explain **what you accomplished**, not just what changed.

### Undoing changes

```bash
rewindex log --limit 10           # 1. find the target
rewindex rewind D1VS v4 --yes     # 2a. rewind one file
rewindex rewind SS3 --yes         # 2b. or rewind entire project state
```

### Before starting work

```bash
rewindex log --limit 20
```

## Rules

- **ALWAYS run `rewindex snap "..."` after finishing a task**
- **Always use `--yes`** with rewind, project remove, project remove-folder
- **Do NOT modify files in `~/.rewindex/`**
- Notes: max 256 characters
- Project names: alphanumeric, dots, hyphens, underscores (max 64 chars)
- Binary files and files > 10 MB are skipped
- Excluded by default: `.env`, `.env.*`, `node_modules/`, `.git/`, `__pycache__/`, `venv/`, `*.log`, `*.tmp`
- Never snapshotted: SSH keys, certificates, `.npmrc`/`.pypirc`/`.netrc`, `*.secret`/`*.token`/`*.password`, `*.kubeconfig`

## Edge Cases

| Scenario | What happens |
|---|---|
| `snap "msg"` but no files changed | Nothing created |
| `rewind SN` on a deleted file | Restores the file to disk |
| `rewind SN` where snapshot is a deletion | Removes the file from disk |
| `rewind SS` (session) | Full project-state restore — all tracked files |
| File > 10 MB or binary | Silently skipped |
| Low disk (<100 MB free) | Snapshots paused, warning shown |
