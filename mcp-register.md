# Rewindex — MCP Server Registration

Register Rewindex as an MCP server so your AI agent can call Rewindex tools directly.

---

## Claude Code

```bash
claude mcp add rewindex -s user rewindex mcp
```

Config saved to `~/.claude.json`. Verify: `claude mcp list` → `rewindex: ✓ Connected`

[Claude Code MCP docs](https://code.claude.com/docs/en/mcp)

---

## Cursor, Windsurf, Cline, and most other agents

Add to the agent's config file:

```json
{
  "mcpServers": {
    "rewindex": {
      "command": "rewindex",
      "args": ["mcp"],
      "type": "stdio"
    }
  }
}
```

| Agent | Config file | Docs |
|-------|------------|------|
| **Cursor** | `~/.cursor/mcp.json` (global) or `.cursor/mcp.json` (project) | [cursor.com/docs/context/mcp](https://cursor.com/docs/context/mcp) |
| **Windsurf** | `~/.codeium/windsurf/mcp_config.json` | [docs.windsurf.com/windsurf/cascade/mcp](https://docs.windsurf.com/windsurf/cascade/mcp) |
| **Cline** | macOS: `~/Library/Application Support/Code/User/globalStorage/saoudrizwan.claude-dev/settings/cline_mcp_settings.json` | [Cline MCP Server Management](https://github.com/cline/cline/wiki/9.2-mcp-server-management) |

---

## OpenCode

Add to `~/.config/opencode/opencode.json` (global) or `opencode.json` in project root:

```json
{
  "mcp": {
    "rewindex": {
      "type": "local",
      "command": ["rewindex", "mcp"],
      "enabled": true
    }
  }
}
```

[opencode.ai/docs/mcp-servers](https://opencode.ai/docs/mcp-servers/)

---

## OpenClaw

Add to `~/.openclaw/openclaw.json`:

```json
{
  "mcp": {
    "servers": {
      "rewindex": {
        "command": "rewindex",
        "args": ["mcp"]
      }
    }
  }
}
```

After editing, run `openclaw gateway restart` to apply. Verify: `openclaw mcp list`

[docs.openclaw.ai/cli/mcp](https://docs.openclaw.ai/cli/mcp)

---

## Nanobot

Add to `nanobot.yaml` in your project directory (or `.nanobot/mcp-servers.yaml`):

```yaml
mcpServers:
  rewindex:
    command: "rewindex"
    args:
      - mcp
```

[DeepWiki — Nanobot MCP Server Configuration](https://deepwiki.com/nanobot-ai/nanobot/2.4-mcp-server-configuration)

---

## Hermes (NousResearch)

Add to `~/.hermes/config.yaml`:

```yaml
mcp_servers:
  rewindex:
    command: "rewindex"
    args: ["mcp"]
```

[hermes-agent.nousresearch.com/docs/user-guide/features/mcp](https://hermes-agent.nousresearch.com/docs/user-guide/features/mcp)
