# KnowMine Claude Code Plugin

> Save notes and decisions during AI conversations. Search them later by meaning, not keywords.

KnowMine gives Claude Code a long-term memory via MCP. Everything you tell it to remember is stored in your personal knowledge base and retrievable by semantic search — across conversations, sessions, and AI tools.

## Quick Install

Run in your terminal:

```bash
# 1. Register this repo as a marketplace
claude plugin marketplace add YIING99/knowmine-claude-plugin

# 2. Install the plugin (user scope = global)
claude plugin install knowmine
```

Restart your Claude Code session after install. Claude Code will prompt you to enter your KnowMine API key (get one at [knowmine.ai/settings/mcp](https://knowmine.ai/settings/mcp)) — it's stored in your system keychain, no manual MCP JSON config needed.

## What You Can Do

### MCP Tools

| Tool | Example |
|------|---------|
| `save_memory` | "Remember: we use Postgres, not Mongo" |
| `recall_memory` | "What database did we decide on?" |
| `add_knowledge` | Save a code snippet, article, or insight |
| `search_my_knowledge` | "Find my notes on deployment" |
| `get_soul` | Generate your AI profile from your knowledge base |

KnowMine currently exposes 16 MCP tools across knowledge CRUD, memory, Soul workflows, and growth summaries.

Full MCP tool reference: [skills/knowmine/SKILL.md](skills/knowmine/SKILL.md)

### Skills (AI-invoked)

| Skill | What it does |
|-------|-------------|
| `conversation-distill` | At the end of any meaningful conversation, Claude scans the full session, classifies outputs into 6 categories (insights / decisions / facts / action items / open questions / observations), shows you a confirmation list, and batch-writes to KnowMine after you approve. Trigger manually: say "distill", "wrap up", or "save this session". |

→ [skills/conversation-distill/SKILL.md](skills/conversation-distill/SKILL.md)

## Why KnowMine?

- **Semantic search** — finds the right note even when you use different words
- **Works everywhere** — same knowledge base across Claude Code, ChatGPT, and any MCP client
- **Your data** — stored on your account, not in the AI's context
- **MCP-native** — no browser extension, no copy-paste, just natural conversation

## Manual MCP Setup

If you prefer not to use the plugin system, add to your `.mcp.json`:

```json
{
  "mcpServers": {
    "knowmine": {
      "type": "http",
      "url": "https://knowmine.ai/api/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_KNOWMINE_API_KEY"
      }
    }
  }
}
```

## Links

- **Website**: [knowmine.ai](https://knowmine.ai)
- **Get API key**: [knowmine.ai/settings/mcp](https://knowmine.ai/settings/mcp)
- **Install manifest**: [knowmine.ai/openclaw-skill.json](https://knowmine.ai/openclaw-skill.json)
- **Health check**: [knowmine.ai/api/mcp/health](https://knowmine.ai/api/mcp/health)
- **Main repo**: [github.com/YIING99/knowmine](https://github.com/YIING99/knowmine)
- **Issues**: [github.com/YIING99/knowmine-claude-plugin/issues](https://github.com/YIING99/knowmine-claude-plugin/issues)

## License

MIT — see [LICENSE](LICENSE)
