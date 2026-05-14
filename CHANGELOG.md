# Rewindex Changelog

## v1.3.0 — 2026-05-12

- Built-in dashboard: `rewindex web` starts the dashboard on port 9009
- Custom port support: `rewindex web -p 8080`
- Dashboard is now included in the core package. No separate install needed.
- Edit session, flag, and pre-compute settings directly from the dashboard
- Improved loading experience with skeleton placeholders
- Redesigned rewind confirmation with clear warnings and save target info
- Instant feedback on daemon start/stop/restart actions
- Pro-only settings are now visually marked and locked for free users
- Fixed a memory issue in the background daemon

---

## v1.2.1 — 2026-05-10

- Dashboard: flags page for viewing, creating, and deleting version milestones
- Dashboard: inline diff viewer with syntax highlighting
- Dashboard: related files view (files that frequently change together)
- Sessions and snapshots now show lines added/removed
- Automatic database migration for existing workspaces

---

## v1.2.0 — 2026-05-07

- Added MCP server support — AI agents can now call Rewindex tools directly without extra setup
- Setup guide now covers Claude Code, Cursor, Windsurf, Cline, OpenCode, OpenClaw, Nanobot, and Hermes
- Simplified AI agent setup: no SKILL.md or memory configuration needed

---

## v1.1.0 — 2026-05-07

- Added flag system — mark any snapshot with a named flag for easy reference and navigation

---

## v1.0.5 — 2026-04-28

- License activation now uses direct API (faster, more reliable)
- Added `rewindex member --deactivate` to release license for use on another device
- Projects exceeding free tier limit now show as paused
- Paused projects are now blocked from snap, note, and rewind
- Improved license validation and error handling
- Improved error messages for license activation

---

## v1.0.4 — 2026-04-28

- Improved snapshot history output
- Documentation updates

---

## v1.0.2 — 2026-04-27

- Bug fixes and stability improvements
- Documentation updates

---

## v1.0.1 — 2026-04-26

- Fixed an issue where file changes could be missed in some editors
- Documentation improvements

---

## v1.0.0 — 2026-04-24

Complete rewrite.

- New snapshot engine with faster rewind and smaller storage
- Redesigned CLI with clearer output and new commands
- Multi-folder project support
- Free and Pro tiers
- Updated SKILL.md for AI agents

---

## v0.2.8 — 2026-04-18

- Per-project exclude patterns
- Snapshot recovery improvements

---

## v0.2.7 — 2026-04-18

- Log output improvements

---

## v0.2.6 — 2026-04-18

- Project auto-detect improvements

---

## v0.2.5 — 2026-04-18

- Smarter project detection from current directory
- New `restart` command
- General improvements

---

## v0.2.4 — 2026-04-17

- Redesigned log output
- New `--reverse` option

---

## v0.2.3 — 2026-04-17

- Bug fixes and output improvements

---

## v0.2.2 — 2026-04-17

- Bug fix

---

## v0.2.1 — 2026-04-17

- Session-level annotations
- Documentation updates

---

## v0.2.0 — 2026-04-17

- Session view improvements
- SKILL.md updates

---

## v0.1.8 — 2026-04-17

- Deletion detection and full project-state rewind
- File IDs and session-level snapshots
- Several bug fixes

---

## v0.1.7 — 2026-04-16

- Rewind accuracy fixes
- UX and error message improvements
- Security improvements

---

## v0.1.6 — 2026-04-16

- New `snap` with message and `note --edit`
- Bug fixes

---

## v0.1.5 — 2026-04-16

- Structured ID system (SN, SS, PJ prefixes)
- Help and output improvements

---

## v0.1.4 — 2026-04-16

- Version check command
- Welcome screen

---

## v0.1.3 — 2026-04-16

- Multi-project support
- Project rename

---

## v0.1.2 — 2026-04-16

- Package description and README updates

---

## v0.1.1 — 2026-04-16

- Added install methods and platform info

---

## v0.1.0 — 2026-04-16

First public release.
