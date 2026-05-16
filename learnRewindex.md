# Learn Rewindex

Everything you need to know about Rewindex as a user.

Rewindex is an auto-snapshot tool that works alongside your AI agent. Every time a file is saved, Rewindex captures it automatically. No commits, no manual steps. When something breaks, tell your AI to rewind and it rolls back instantly.

Everything runs locally. No cloud, no account required.

---

## Table of Contents

- [How It Works](#how-it-works)
- [The Dashboard](#the-dashboard)
- [Sessions and Snapshots](#sessions-and-snapshots)
- [Notes](#notes)
- [Flags (Version Milestones)](#flags-version-milestones)
- [Rewind](#rewind)
- [Generating a Changelog](#generating-a-changelog)
- [How Snapshots Are Stored](#how-snapshots-are-stored)
- [Auto-Trim](#auto-trim)
- [Settings Reference](#settings-reference)
- [ID Reference](#id-reference)
- [Storage and Cleanup](#storage-and-cleanup)
- [Working with Your AI](#working-with-your-ai)
- [FAQ](#faq)

---

## How It Works

Once installed, Rewindex runs a background daemon that watches your workspace folders.

1. **You or your AI saves a file.** Rewindex detects the change and stores a snapshot as a compressed diff.
2. **Changes are grouped into sessions.** Saves that happen within 20 seconds of each other belong to the same session. A gap longer than 20 seconds starts a new session.
3. **Your AI reads the history before working.** At the start of a task, your AI checks the Rewindex log to understand recent changes.
4. **Your AI notes what it did.** After finishing work, your AI writes a note on the session describing what changed.

This happens automatically through MCP (Model Context Protocol). You don't need to run commands or configure anything beyond the initial setup.

---

## The Dashboard

```bash
rewindex web
```

Open [http://localhost:9009](http://localhost:9009) in your browser. If Rewindex is running on a remote server or VPS, use your server's IP address instead (e.g. `http://your-server-ip:9009`).

This is the main way you interact with Rewindex directly. From the dashboard you can:

- Browse all sessions and snapshots
- View file diffs with syntax highlighting
- Rewind files or entire sessions
- Create, edit, and delete flags
- See related files (files that frequently change together)
- Check lines added and removed per file
- Edit settings (session limits, pre-compute, exclusions)
- Check daemon status and disk usage

Everything else goes through your AI.

---

## Sessions and Snapshots

A **snapshot** is a single file change captured by Rewindex. Every time a file is saved, one snapshot is created.

A **session** is a group of snapshots that belong together. Rewindex groups them automatically: if file changes happen within 20 seconds of each other, they go into the same session. A gap longer than 20 seconds, or your AI calling the snap command, closes the current session. The next change starts a new one.

Each session has:

| Field | Description |
|---|---|
| Session ID | e.g. SS5, SS12 |
| Files changed | List of all files that were modified |
| Lines added/removed | Per file and total |
| Note | Optional description of what was done |
| Timestamp | When the session started |

When your AI completes a task involving multiple file edits, all those changes end up in one or a few sessions. This makes it easy to see what happened and to rewind an entire task at once instead of reverting individual files.

---

## Notes

Notes are short text descriptions attached to sessions, file versions, or individual snapshots. They answer the question: "What happened here?"

Your AI should note every session after completing work. If it doesn't, just ask:

> "Note the latest Rewindex session."

Notes are visible in the dashboard and in the session log. Maximum 256 characters per note. Writing a new note on the same target replaces the old one.

---

## Flags (Version Milestones)

Flags mark specific sessions as named versions. Think of them as lightweight releases or checkpoints that you can return to later.

Each flag has:

| Field | Description |
|---|---|
| Version | e.g. v1.0.0 (auto-incremented or set manually) |
| Title | Short name for the milestone |
| Description | Longer explanation, supports Markdown |
| Session | The session this flag is attached to |

**When to flag:**

- After completing a feature that works and has been tested
- Before a deploy or release
- At any point you'd want to return to later
- When the system could be shipped or demoed

**When not to flag:**

- After minor edits or typo fixes
- Mid-way through unfinished work
- Every single session

Unlike notes, which your AI creates automatically after each session, flags are not automatic. You need to tell your AI when you want a flag created. Your AI already knows how to write the version, title, and description based on the work context. This is by design: flags are quality gates, not quantity markers.

You can move a flag to a different session, edit its title and description, or delete it. All manageable from the dashboard or through your AI.

---

## Rewind

Rewind restores your files to a previous state. There are three levels:

### Session rewind (recommended)

Restores all files in a session to their state at that point. This is the safest approach because files that changed together get restored together, avoiding partial-state bugs.

> "Rewind to session SS5."

### File rewind

Restores a single file to a specific version. Use only when you know the file changed independently of other files.

> "Rewind file D1VS to version 3."

### Relative rewind

Undoes the last N snapshots across all affected files. Useful when you know how many steps to go back but not the exact session.

> "Undo the last 3 snapshots."

Every rewind is itself recorded as a new snapshot with a note describing what was rewound. So you can always undo a rewind.

---

## Generating a Changelog

Flags double as a changelog system. If you've been flagging milestones throughout development, you can export them as a formatted changelog.

Two ways to export:

- **From the dashboard:** open the flags page and export from there
- **Through your AI:** tell it to export, and it will generate the changelog and save it for you

> "Export changelog from Rewindex."

The output includes version numbers, titles, descriptions, and dates. You can also filter by starting session to export only recent flags.

This means your changelog builds itself as you work, instead of trying to reconstruct everything at release time.

---

## How Snapshots Are Stored

Understanding how Rewindex stores data helps you make better decisions about settings.

### Diffs, not full copies

When a file changes, Rewindex stores only the **diff** (the difference from the previous version), not the entire file. This keeps storage very small but means rewinding a file requires **reconstructing** it by replaying diffs forward from a starting point.

### Fullbase

A **fullbase** is a full copy of a file stored at a specific version. It serves as a starting point for reconstruction. Without any fullbase, rewinding to version 50 would mean replaying all 50 diffs from the very beginning.

Fullbase snapshots are created automatically:

| Type | When | Purpose |
|---|---|---|
| Original | First time a file is tracked (v1) | Starting point for all future diffs |
| Trim base | When old sessions are auto-trimmed | Ensures rewind still works after old diffs are removed |
| Pre-compute (Pro) | Every `cut` sessions | Limits how many diffs need to be replayed |

### Reconstruction example

A file has 50 versions. The nearest fullbase is at v1 (Original).

- To rewind to v45, Rewindex replays diffs from v1 through v45. That is 44 diffs.
- With a pre-compute fullbase at v40, Rewindex starts from v40 and replays only 5 diffs.

The more fullbase points exist, the faster rewind gets.

### Version labels

When viewing a file's history in the dashboard, each version may show a label:

| Label | Meaning |
|---|---|
| [Original] | v1, first time tracked (full copy stored) |
| [Fullbase] | Full copy created after trim promote |
| [Trim base] | Full copy stored as base after trim |
| [Pre-compute] | Full copy pre-computed for faster rewind (Pro) |

No label means the version is stored as a diff.

---

## Auto-Trim

Rewindex automatically manages how many sessions are kept. When the session count reaches the `max` limit, the oldest sessions are removed in the background.

Before removing any sessions, Rewindex writes a fullbase (trim base) for each affected file. This ensures rewind still works for all remaining sessions.

Trimming runs in the background and does not affect performance. See the Settings Reference below for how to configure the limits.

---

## Settings Reference

All settings are editable from the dashboard.

### Session limits

Three values control how many sessions Rewindex keeps:

| Setting | Default | Description |
|---|---|---|
| `safe` | 400 | Number of sessions always protected from auto-trim |
| `max` | 500 | Total session count that triggers auto-trim |
| `cut` | 100 | Calculated as `max - safe`. Number of oldest sessions removed per trim cycle |

**How a trim cycle works:**

1. Sessions accumulate as you work
2. When session count reaches `max` (500), auto-trim starts
3. The oldest `cut` (100) sessions are removed
4. A fullbase is written for each affected file before removal
5. Session count drops back to `safe` (400)
6. Cycle repeats

**What happens when you change these values:**

| Change | Effect |
|---|---|
| Increase `safe` | More history kept, more disk usage |
| Increase `max` | Trim runs less often, but removes more sessions at once |
| Decrease `cut` (by bringing `max` closer to `safe`) | Trim runs more often, each trim is lighter |

### Pre-compute fullbase (Pro only)

| Setting | Options | Default |
|---|---|---|
| precompute | on / off | off |

When enabled, Rewindex writes a full copy of each changed file every `cut` sessions. This limits the worst-case number of diffs replayed during rewind.

With default settings (`cut = 100`):

| Pre-compute | Worst-case diffs to replay |
|---|---|
| Off | Up to 400+ (from nearest Original or Trim base) |
| On | At most 100 |

This setting matters for workspaces with long histories where rewind speed becomes noticeable. For most workspaces, the default (off) is fine.

### Exclusion patterns

Files matching exclusion patterns are never snapshotted. You can set exclusions globally (all workspaces) or per workspace from the dashboard.

**Default exclusions:**

```
.env, .env.*, node_modules/, .git/, __pycache__/,
venv/, .venv/, *.log, *.tmp
```

**Security exclusions (always active, cannot be removed):**

```
*.secret, credentials, id_rsa, id_ed25519, *.pem, *.key,
*.p12, .npmrc, .pypirc, .netrc, *.password, *.token, *.kubeconfig
```

### Config file

All settings are stored at `~/.rewindex/.rewindex.config.yaml`. You can edit this file directly, but using the dashboard is recommended.

---

## ID Reference

Rewindex uses short IDs to identify things. You will see these in the dashboard and in conversations with your AI.

| ID format | What it represents | Example |
|---|---|---|
| `SS` + number | Session (a group of snapshots) | SS3, SS12 |
| `SN` + number | Snap (a single file change) | SN42, SN7 |
| 4-character code | FileID (stable for the file's lifetime) | D1VS, U5XD |
| `v` + number | File version number | v1, v3 |
| `WS` + number | Workspace | WS1, WS2 |

`latest` can be used anywhere a version or session ID is expected.

---

## Storage and Cleanup

All data is stored locally at `~/.rewindex/`.

Snapshots are compressed diffs, so storage usage is minimal. Check the dashboard for exact disk usage numbers.

When a workspace is removed, its snapshot data moves to trash. You can restore or permanently delete trashed workspaces through your AI chat.

---

## Working with Your AI

Your AI connects to Rewindex through MCP. Once the MCP server is registered, your AI has access to all Rewindex tools automatically.

### What your AI does on its own

- Creates snapshots and notes sessions after making changes
- Uses session-level rewind when something goes wrong

### What you should tell your AI

These are just examples. You do not need to use these exact words. Your AI will understand as long as the intent is clear (assuming you are using a capable enough model).

**When starting a new project:**
> "Set up Rewindex for this workspace."

If you work with multiple AI agents on different folders at the same time, each agent can create its own workspace. For example, AI agent A working on `/projects/frontend` creates workspace "frontend", while AI agent B working on `/projects/backend` creates workspace "backend". Each workspace tracks its own snapshots and sessions independently.

**When your AI forgets to note a session:**
> "Note the latest Rewindex session."

Your AI will check what changed and write an appropriate note. AI agents sometimes skip the note step. Remind them when you notice a session without a note.

**When you reach a milestone:**
> "Flag this session as v1.0.85."

Your AI does not create flags on its own. You need to tell it when you want one. Your AI already knows how to write the version, title, and description based on the work context.

**When starting a new chat session:**
> "Always snap and note Rewindex after making changes."

Remind your AI at the start of each chat to snap and note every time it finishes editing files. This ensures nothing is lost between conversations.

**When something breaks:**
> "Rewind to the last working session."

Your AI will check the log, find the right session, and restore everything.

---

## FAQ

| Question | Answer |
|---|---|
| Does Rewindex modify my files? | Only when you explicitly rewind. |
| Does it send data anywhere? | No. Everything stays local on your machine. |
| Does it work with Git? | Yes. Rewindex captures the changes between commits. Use Git for version control, Rewindex for the work in between. |
| Can I use it without AI agents? | Yes, but you will not be able to note sessions through chat. You can still flag and rewind from the dashboard. Rewindex works best with AI agents through MCP. |
| How much disk space does it use? | Very little. Diffs are compressed. Check the dashboard for exact numbers. |
| What platforms are supported? | macOS, Linux, and Windows (beta). |
| Is it free? | Free forever. No account required. Pro tier adds more workspaces and pre-compute fullbase. See [rewindex.org](https://rewindex.org). |
