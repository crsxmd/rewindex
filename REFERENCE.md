# Rewindex — Command Reference

> Reference version: 1.0.3

Full command documentation. For installation see [README](README.md). For AI agent setup see [AI-SETUP.md](AI-SETUP.md).

---

## Activate for your AI agent

**1. Install Rewindex**

```bash
pipx install rewindex
rewindex init --project [name] --folder [/path/to/project]
```

**2. Save SKILL.md to your agent's instructions folder**

Copy the contents of [SKILL.md](SKILL.md) and paste it into the correct path for your agent — see [AI-SETUP.md](AI-SETUP.md) for the path per agent type.

**3. Add this to your agent's memory**

```
This project uses Rewindex — a version control designed for AI agents.
It acts as a safety net: every file change is versioned automatically,
so you can rewind to a known good state and fix mistakes faster with
fewer wasted steps.

- After editing any file → snap with a note describing what changed
- When user indicates a mistake (broke, wrong, revert, undo,
  "it was better before", error after your edit) → rewind to last
  known good state, then fix from there
  only edit directly if the fix is one obvious line with no side effects
- If unsure which version to rewind to → check log first
- When starting a new project or working in a folder not yet in Rewindex →
  ask the user to add the project to Rewindex first
```

---

## Project Selection

All commands auto-detect the active project from your current working directory. No need to specify `-p` if you're already inside the project folder.

```bash
cd ~/projects/my-app
rewindex snap           # auto-detects my-app
rewindex log            # shows my-app history
```

Use `-p [project-name]` to target a different project regardless of CWD. If you're outside all project folders and have multiple projects, an interactive picker appears.

---

## ID Format

| Type | Format | Example |
|---|---|---|
| Session | `SS` + integer (no zero-padding) | `SS3`, `SS12` |
| Snap | `SN` + integer (no zero-padding) | `SN42`, `SN7` |
| FileID | 4-char alphanumeric, stable for the file's lifetime | `D1VS`, `U5XD` |
| File version | `v` + integer | `v1`, `v3` |
| Project | `PJ` + integer | `PJ1`, `PJ2` |

`latest` is accepted anywhere a version or session ID is expected.

---

## Command Tree

```
rewindex
├── init                                    Set up a project and start the daemon
│   ├── --project <name>
│   ├── --folder <path>
│   └── --license <key>                     Pro license key (optional)
│
├── start                                   Start the daemon
├── stop                                    Stop the daemon
├── restart                                 Restart the daemon
├── status                                  Show daemon status, active projects, and disk usage
│
├── snap ["message"]                        Snapshot all changed files now
│
├── log                                     Session list (default view)
│   ├── sn                                  Flat list — all snapshots
│   ├── [FileID]                            Version history for a specific file
│   ├── [SN<n>]                             Resolve snap → show file version history
│   ├── [SS<n>]                             Full project state at a session
│   ├── --detail                            Show all versions per session
│   ├── --full                              Full project state across all sessions
│   ├── --reverse / -r                      Oldest first
│   ├── --limit / -l / -n <n>              Show last N sessions (default: 15)
│   ├── --since <DD/MM/YY>                  From date onward
│   ├── --folder <path>                     Filter by folder
│   ├── --from <SN|SS>                      Start from this ID
│   └── --to <SN|SS>                        End at this ID
│
├── diff <FileID> [v_from] [v_to]           Diff between file versions
│
├── rewind <target> [version] --yes         Restore to a previous state
│   ├── SS<n> --yes                         Full project restore to session state
│   ├── <FileID> v<n> --yes                 Restore one file to a specific version
│   ├── <FileID> latest --yes               Restore one file to latest version
│   └── SN -<n> --yes                       Undo last N snapshots
│
├── note <target> [version] "msg"           Annotate a session, file version, or snap
│   ├── <FileID> [v<n>|latest]
│   ├── SS [<n>|latest]
│   └── SN<n>
│
├── member                                  Show current plan
│   └── --license <key>                     Activate a Pro license key
│
├── setting                                 Show or update session limit settings
│   ├── --safe <n>
│   ├── --max <n>
│   └── precompute on|off                   Toggle pre-compute fullbase (Pro)
│
├── project                                 Manage projects
│   ├── add <name> <folder>                 Add a project
│   ├── remove <name|PJID> [--yes]          Remove a project (moves to trash)
│   ├── rename <name|PJID> <new-name> [--yes]  Rename a project
│   ├── add-folder <name|PJID> <path>       Add a watched folder
│   ├── remove-folder <name|PJID> <path> [--yes]  Remove a watched folder
│   ├── folder add <name|PJID> <path>             Add a watched folder (alt syntax)
│   ├── folder remove <name|PJID> <path> [--yes]  Remove a watched folder (alt syntax)
│   ├── exclude add <pattern> [-p project]  Add exclusion pattern
│   └── exclude remove <pattern> [-p project] Remove exclusion pattern
│
├── cleanup                                 Manage trashed projects
│   ├── --trash                             Show trashed projects
│   ├── --trash --yes                       Permanently delete all trashed projects
│   └── --trash restore <name|PJID>         Restore a trashed project
│
├── update                                  Check for updates
│   ├── --latest                            Upgrade from PyPI
│   ├── --latest --yes                      Upgrade without confirmation
│   └── --sync                              Sync config with current version defaults
│
└── uninstall                               Uninstall Rewindex and remove daemon
    └── --yes                               Skip confirmation (keeps snapshot data)

Global option (snap, log, diff, note, rewind)
└── -p / --project <name|PJID>              Target a specific project (auto-detected from CWD if omitted)
```

---

## Setup

### `rewindex init`

Sets up Rewindex for a project. Starts the daemon automatically on completion.

```bash
rewindex init
```

Interactive mode asks for project name, folder path, and an optional Pro license key.

For non-interactive use (AI agents or scripts):

```bash
# With license
rewindex init --project [name] --folder [/path/to/project] --license [key]

# Free tier
rewindex init --project [name] --folder [/path/to/project]
```

> Safe to re-run — merges with existing config, never overwrites.

---

### `rewindex start` / `rewindex stop` / `rewindex restart`

```bash
rewindex start      # start watching
rewindex stop       # stop watching
rewindex restart    # stop + start (reloads config)
```

Only one daemon runs system-wide — it watches every configured project simultaneously.

---

### `rewindex status`

```bash
rewindex status
```

Shows daemon state, all projects, folder paths, session range, snap count, and last activity.

---

## History

### `rewindex log`

Shows snapshot history grouped into sessions. A **session** is a group of snapshots taken close together.

```bash
rewindex log                        # session list (default)
rewindex log sn                     # flat list of all snapshots
rewindex log D1VS                   # version history for a specific file
rewindex log SN5                    # resolve snap → show file version history
rewindex log SS12                   # full project state at session SS12
rewindex log --detail               # all versions per session
rewindex log --reverse              # oldest first
rewindex log --limit 20
rewindex log --since 18/04/26
rewindex log --folder src/
watch -n 2 "rewindex log --limit 10 -r"   # live monitor
```

When viewing file version history (`rewindex log <FileID>`), each version may show a status label:

| Label | Meaning |
|---|---|
| `[Original]` | v1 — first time this file was tracked |
| `[Fullbase]` | Full copy stored (created after trim promote) |
| `[Trim base]` | Full copy stored as reconstruction base after trim |
| `[Pre-compute]` | Full copy pre-computed for faster rewind (Pro) |

---

### `rewindex diff`

```bash
rewindex diff D1VS             # previous → latest
rewindex diff D1VS v5          # v4 → v5
rewindex diff D1VS v3 v5       # v3 → v5
rewindex diff D1VS v3 latest   # v3 → latest
rewindex diff SS5 SS8          # compare project state between two sessions
rewindex diff SS5              # session state vs current files on disk
```

---

## Snapshots

### `rewindex snap ["message"]`

```bash
rewindex snap                          # snapshot all changed files now
rewindex snap "before major refactor"  # with session note
```

If no files changed, nothing is created.

---

## Rewind & Restore

### `rewindex rewind`

```bash
rewindex rewind SS3 --yes            # restore entire project to SS3 state
rewindex rewind D1VS v4 --yes        # restore one file to version 4
rewindex rewind D1VS latest --yes    # restore one file to latest version
rewindex rewind SN -1 --yes          # undo last 1 snapshot (all affected files)
rewindex rewind SN -5 --yes          # undo last 5 snapshots (all affected files)
```

**Always use `--yes`** — without it, Rewindex waits for confirmation and agents hang.

Every rewind automatically saves a note on the created version(s) describing what was rewound.

---

## Annotate

### `rewindex note`

```bash
rewindex note D1VS v3 "fix: null check"    # specific file version
rewindex note D1VS "refactor done"          # defaults to latest version
rewindex note SS3 "before deploy"           # annotate a session
rewindex note SS latest "checkpoint"        # annotate latest session
rewindex note SS "checkpoint"               # same as SS latest
rewindex note SN42 "daemon auto-snap"       # annotate a snap
```

Running `note` on a target that already has a note replaces it. Max 256 characters.

---

## Membership

### `rewindex member`

```bash
rewindex member                          # show current plan
rewindex member --license [key]          # activate a Pro license key
```

---

## Settings

### `rewindex setting`

```bash
rewindex setting                         # show current settings
rewindex setting --safe 400 --max 500    # update session limits
rewindex setting precompute on           # enable pre-compute fullbase (Pro)
rewindex setting precompute off
```

`safe` = sessions kept safe from trim. `max` = session count that triggers a trim. `cut = max - safe`.

---

### `rewindex setting precompute` (Pro)

```bash
rewindex setting precompute on    # enable
rewindex setting precompute off   # disable
```

Writes a fullbase snapshot every `cut` sessions so rewind starts from a nearby checkpoint instead of reconstructing through all diffs. The interval equals `cut` — no separate setting. Pro tier only.

---

## Project Management

```bash
rewindex project                                              # list all
rewindex project add MyApp ~/projects/my-app                  # add (positional)
rewindex project add --name MyApp --folder ~/projects/my-app  # add (flags)
rewindex project remove my-app --yes                          # remove → moves to trash
rewindex project rename old-name new-name                     # rename
rewindex project add-folder my-app /path/to/folder            # add folder
rewindex project remove-folder my-app /path/to/folder --yes   # remove folder
rewindex project folder add my-app /path/to/folder            # add folder (alt syntax)
rewindex project folder remove my-app /path/to/folder --yes   # remove folder (alt syntax)
rewindex project exclude add "*.log"                          # global exclude
rewindex project exclude add "*.log" -p my-app                # per-project exclude
rewindex project exclude remove "*.log"
```

`project remove` moves snapshots to trash at `~/.rewindex/trash/`. Use `rewindex cleanup --trash` to delete permanently or restore.

---

## Cleanup

```bash
rewindex cleanup --trash                        # show trashed projects
rewindex cleanup --trash restore ApiServer      # restore by name
rewindex cleanup --trash restore PJ2            # restore by PJ ID
rewindex cleanup --trash --yes                  # permanently delete all trashed projects
```

---

## Maintenance

```bash
rewindex update          # check for updates and upgrade
rewindex uninstall       # stop daemon + remove Rewindex (keeps snapshot data by default)
```

---

## Configuration

Config file: `~/.rewindex/.rewindex.config.yaml`

```yaml
projects:
  - name: my-app
    folders:
      - ~/projects/my-app

exclude:
  - ".env"
  - ".env.*"
  - "node_modules/"
  - ".git/"
  - "__pycache__/"
  - "venv/"
  - ".venv/"
  - "*.log"
  - "*.tmp"
```

### Security Exclusions (hardcoded)

These files are **never** snapshotted regardless of config:
`*.secret`, `credentials`, `id_rsa`, `id_ed25519`, `*.pem`, `*.key`, `*.p12`, `.npmrc`, `.pypirc`, `.netrc`, `*.password`, `*.token`, `*.kubeconfig`

---

## Auto-Trim

Rewindex prunes old sessions in the background when the session count reaches `max`. The oldest `cut` sessions are removed, with a fullbase written first so rewind stays fast. Configure with `rewindex setting --safe N --max M`.

---

## Session Grouping

- Snapshots within ≤ 20s of each other → same session
- Gap > 20s → new session

---

## FAQ

**Q: Does Rewindex modify my files?**
A: Never — except when you explicitly run `rewindex rewind`.

**Q: Does it send data anywhere?**
A: No. Everything is stored locally in `~/.rewindex/`.

**Q: How much disk space does it use?**
A: Very little. Diffs are compressed. Run `rewindex status` to check.

**Q: Can I use it without AI agents?**
A: Yes. Manual changes are labeled `manual`.

**Q: Does it work on Windows?**
A: Not yet. Linux and macOS only.
