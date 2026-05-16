# Rewindex

![python](https://img.shields.io/badge/python-3.10%2B-blue)
![license](https://img.shields.io/badge/license-proprietary-red)

**Rewindex — Auto-snapshot tool for AI agents**

Auto-snapshot tool for AI agents. Automatically backs up every file change so you can recover from AI mistakes instantly. Code worry-free. Free and local.

- **Versioning**: snap, rewind, and track every change your AI makes
- **Change visibility**: view diffs, session logs, and full file history
- **Changelog**: flag milestones and export as CHANGELOG.md

Works with any AI agent via MCP. Works with Git. Rewind the code that you didn't commit.

**[rewindex.org](https://rewindex.org)** | [GitHub](https://github.com/crsxmd/rewindex)

> **Want to understand how Rewindex works in depth?** Read [Learn Rewindex](learnRewindex.md).

---

## How it works

1. **Every save is recorded**

   Every time you or your AI saves a file, Rewindex captures a snapshot automatically.

2. **Sessions group your work**

   When your AI finishes a task, all the changes are grouped into one session. Easy to find, easy to rewind.

3. **AI notes what it did**

   When your AI finishes a task, it writes a note on the session. You can add your own notes too.

4. **Rewind without guessing**

   When something breaks, the full log is right there. Your AI knows exactly where to go back to.

### Try it! Then ask your AI if it likes it.

---

## Quick Start

> **Supported platforms: macOS, Linux, and Windows (beta).**

Requires Python 3.10+.

---

### AI agent setup (recommended)

Paste this into your AI agent's chat. It handles everything including install, MCP setup, and init:

```text
Read https://raw.githubusercontent.com/crsxmd/rewindex/main/AI-SETUP.md and follow the setup instructions.
```

---

### Manual setup

**1. Install**
```bash
pipx install rewindex   # recommended
pip install rewindex    # alternative
uv tool install rewindex
```

**2. Add as MCP server** — see [mcp-register.md](mcp-register.md) for instructions per agent (Claude Code, Cursor, Windsurf, OpenCode, Hermes, and others)

**3. Initialize your workspace**
```bash
rewindex init           # interactive — asks for workspace name and folder
```


---

## Dashboard

```bash
rewindex web
```

Open [http://localhost:9009](http://localhost:9009) — browse snapshots, view diffs, rewind files, manage flags and settings. Included in the package, no extra install needed.

---

## Pricing

Free forever. No account required.

Want to upgrade? Check [rewindex.org](https://rewindex.org)

---

## Documentation

- [Learn Rewindex](learnRewindex.md): everything you need to know about Rewindex as a user
- [AI Agent Setup](AI-SETUP.md): install guide for AI agents with per-agent MCP setup

---

## License

Proprietary. See [LICENSE](https://github.com/crsxmd/rewindex/blob/main/LICENSE) for details.
