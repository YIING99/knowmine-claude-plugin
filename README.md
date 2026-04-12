# KnowMine Claude Code Plugin

> Save notes and decisions during AI conversations. Search them later by meaning, not keywords.

KnowMine gives Claude Code a long-term memory via MCP. Everything you tell it to remember is stored in your personal knowledge base and retrievable by semantic search — across conversations, sessions, and AI tools.

## Quick Install

```
# In Claude Code:
/plugin install knowmine
```

Or from the GitHub source:

```
/plugin marketplace add github:YIING99/knowmine-claude-plugin
```

Claude Code will prompt you to enter your KnowMine API key (get one at [knowmine.ai/settings/mcp](https://knowmine.ai/settings/mcp)). The key is stored securely in your system keychain. No manual config needed.

## What You Can Do

### MCP Tools (always available)

| Tool | Example |
|------|---------|
| `save_memory` | "Remember: we use Postgres, not Mongo" |
| `recall_memory` | "What database did we decide on?" |
| `add_knowledge` | Save a code snippet, article, or insight |
| `search_my_knowledge` | "Find my notes on deployment" |
| `get_soul` | Generate your AI profile from your knowledge base |

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
- **Main repo**: [github.com/YIING99/knowmine](https://github.com/YIING99/knowmine)
- **Issues**: [github.com/YIING99/knowmine-claude-plugin/issues](https://github.com/YIING99/knowmine-claude-plugin/issues)

## License

MIT — see [LICENSE](LICENSE)
