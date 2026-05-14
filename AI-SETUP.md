# Rewindex — AI Agent Setup

This guide is for AI agents installing and configuring Rewindex.

---

## Setup Steps

1. Install Rewindex:
   ```bash
   pipx install rewindex
   ```
   If pipx is not available: `pip install rewindex`

2. **Register as MCP server** — see [mcp-register.md](mcp-register.md) for instructions per agent (Claude Code, Cursor, Windsurf, OpenCode, Hermes, and others)

3. Set up the workspace:

   **If you already know the workspace name and folder** (e.g. from CWD or recent context) — suggest instead of asking:

   > "I'll set up Rewindex for this workspace:
   > - Name: `my-app`
   > - Folder: `/path/to/my-app`
   >
   > Is that correct? If not, let me know the name, folder path, and Pro license key (if any) all at once."

   If the user confirms, run:
   ```bash
   rewindex init --workspace [name] --folder [/path/to/workspace]
   ```

   **If the user says no, or you don't know the workspace** — ask in a single message:

   > "Please provide:
   > 1. Workspace name
   > 2. Folder path to watch
   > 3. Pro license key (optional — leave blank if on Free tier)"

   Then run:
   ```bash
   # With license
   rewindex init --workspace [name] --folder [/path/to/workspace] --license [key]

   # Without license (Free tier)
   rewindex init --workspace [name] --folder [/path/to/workspace]
   ```

4. Tell the user a summary: how you installed it (pipx or pip), the workspace name, and the folder path you set up

---

## Reference

- Product overview and install guide: [README.md](README.md)
- User guide: [learnRewindex.md](learnRewindex.md)
