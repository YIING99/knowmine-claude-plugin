---
name: knowmine
description: "Save notes, decisions, and insights with semantic search via MCP. Use this skill when you want to remember things across conversations, search past knowledge by meaning, save dev logs or learning notes, or carry context between AI sessions."
---

# KnowMine — Personal Knowledge Base for AI Agents

Save notes, decisions, and insights. Search them later by meaning, not keywords.

KnowMine is a remote MCP server that gives your AI agent a personal knowledge base with semantic search. Your agent can save knowledge entries during conversations and retrieve them later using natural language queries. All data is stored per-user with full isolation.

## Setup

### 1. Get your API key

Sign up at [knowmine.ai](https://knowmine.ai), go to Settings → MCP, and copy your key.

### 2. Install via Claude Code Plugin

```bash
# Set your API key first
export KNOWMINE_API_KEY=km_mcp_your_key_here

# Install the plugin (registers MCP server automatically)
/plugin marketplace add github:YIING99/knowmine-claude-plugin
```

The plugin auto-registers the KnowMine MCP server — no manual config needed.

### 3. Manual MCP setup (alternative)

If you prefer to configure manually, add to your `.mcp.json`:

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

## Tools (11)

### Knowledge CRUD

| Tool | What it does |
|------|-------------|
| `add_knowledge` | Save a note, insight, dev log, article, or reflection. Auto-generates title and tags. |
| `search_my_knowledge` | Search your knowledge base by meaning. Supports type and tag filters. |
| `get_knowledge` | Get full content of one entry by ID. |
| `update_knowledge` | Edit title, content, type, or tags of an entry. |
| `delete_knowledge` | Remove an entry. |
| `get_related_knowledge` | Find entries related to a given entry by semantic similarity. |
| `list_folders` | List your folders and recent entries in each. |

### Memory

| Tool | What it does |
|------|-------------|
| `save_memory` | Save a decision, lesson, insight, or preference from a conversation. |
| `recall_memory` | Search past memories by natural language. Filter by type. |

### Analysis

| Tool | What it does |
|------|-------------|
| `get_soul` | Generate a user profile summary based on your knowledge. Exportable as a system prompt. |
| `get_insight` | Analyze your knowledge for patterns — frequent topics, recurring themes. |

## Example Usage

**Save a decision during a coding session:**
```
save_memory: "Chose PostgreSQL over MongoDB for the new project — need ACID transactions and our team knows SQL well"
```

**Find it later in a new session:**
```
recall_memory: "database decision for new project"
→ Returns the PostgreSQL decision with full context
```

**Search across all knowledge:**
```
search_my_knowledge: "deployment strategies"
→ Returns matching notes ranked by relevance, even if you never used the word "deployment" in the original entry
```

## How It Works

- **Search**: pgvector semantic similarity on PostgreSQL (not keyword matching)
- **Embeddings**: OpenAI text-embedding-3-small, auto-generated on save
- **Protocol**: MCP over Streamable HTTP
- **Auth**: API key via Bearer token
- **Data isolation**: Each API key maps to one user account
- **Network**: All requests go to `knowmine.ai` only (no third-party data sharing)
- **Rate limit**: 60 requests/minute

## Source Code

- GitHub: [github.com/YIING99/knowmine](https://github.com/YIING99/knowmine)
- Plugin: [github.com/YIING99/knowmine-claude-plugin](https://github.com/YIING99/knowmine-claude-plugin)
- Website: [knowmine.ai](https://knowmine.ai)
- Health check: [knowmine.ai/api/mcp/health](https://knowmine.ai/api/mcp/health)

---

Built by [YIING99](https://github.com/YIING99)
