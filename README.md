# Rewindex

![python](https://img.shields.io/badge/python-3.10%2B-blue)
![license](https://img.shields.io/badge/license-proprietary-red)

**Rewindex — Auto-snapshot tool for AI agents**

- Auto-snapshot every file you or your AI touches
- Mistake? Check the log, rewind to a known good state
- SKILL.md teaches your AI how to use it. Memory makes sure it always does.
- No cloud. Your data stays local.

Works with Git. Rewind the code that you didn't commit.

**[rewindex.org](https://rewindex.org)** | [GitHub](https://github.com/crsxmd/rewindex)

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

> **Supported platforms: macOS and Linux only.**

Requires Python 3.10+.

---

### AI agent setup (recommended)

Paste this into your AI agent's chat. It handles everything including install, init, SKILL.md setup, and memory:

```text
Read https://raw.githubusercontent.com/crsxmd/rewindex/main/AI-SETUP.md and follow the setup instructions.
```

After setup, verify that [`SKILL.md`](SKILL.md) was saved to the correct location for your agent type — see [AI-SETUP.md](AI-SETUP.md) for the expected path.

---

### Manual setup

**1. Install**
```bash
pipx install rewindex   # recommended
pip install rewindex    # alternative
uv tool install rewindex
```

**2. Initialize your project**
```bash
rewindex init           # interactive — asks for project name and folder
```

**3. Save SKILL.md**

Copy the contents of [SKILL.md](SKILL.md) and paste it into the correct path for your agent — see [AI-SETUP.md](AI-SETUP.md) for the path per agent type.

**4. Add this to your agent's memory**

Copy and paste this into your agent's chat:

```
Save this to your memory:

Every project I work on uses Rewindex — a version control designed for AI agents.
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

## Pricing

Free forever. No account required.

Want to upgrade? Check [rewindex.org](https://rewindex.org)

---

## Documentation

- [Command Reference](REFERENCE.md): all commands, options, and examples
- [AI Agent Setup](AI-SETUP.md): install guide for AI agents with per-agent SKILL.md paths
- [SKILL.md](SKILL.md): workflow rules for AI agents

---

## License

Proprietary. See [LICENSE](https://github.com/crsxmd/rewindex/blob/main/LICENSE) for details.
