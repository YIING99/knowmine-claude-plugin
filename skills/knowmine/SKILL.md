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

Run in your terminal (NOT as a slash command in Claude):

```bash
# Register this repo as a marketplace
claude plugin marketplace add YIING99/knowmine-claude-plugin

# Install the plugin at user scope (global)
claude plugin install knowmine
```

After install, restart your Claude Code session. On first load the plugin prompts you for your `KNOWMINE_API_KEY` and stores it in your system keychain — no environment variables or manual MCP JSON needed.

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

## Tools (16)

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
| `get_growth_summary` | Summarize knowledge growth, active folders, tags, and clusters. |

### Soul Workflow

| Tool | What it does |
|------|-------------|
| `generate_soul` | Generate or refresh a draft Soul profile from your knowledge and memories. |
| `commit_soul` | Apply a previously generated Soul draft after confirmation. |
| `import_soul_context` | Import external memory text, such as CLAUDE.md or ChatGPT memory exports, into Soul signals. |
| `observe_user_trait` | Record a stable user trait observation as a pending Soul signal. |

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
- **Network**: Client requests go to `knowmine.ai` over HTTPS
- **Data processing**: KnowMine may use managed providers such as OpenAI for embeddings and AI analysis on the backend
- **Rate limit**: 60 requests/minute

## Source Code

- GitHub: [github.com/YIING99/knowmine](https://github.com/YIING99/knowmine)
- Plugin: [github.com/YIING99/knowmine-claude-plugin](https://github.com/YIING99/knowmine-claude-plugin)
- Website: [knowmine.ai](https://knowmine.ai)
- Install manifest: [knowmine.ai/openclaw-skill.json](https://knowmine.ai/openclaw-skill.json)
- Health check: [knowmine.ai/api/mcp/health](https://knowmine.ai/api/mcp/health)

---

Built by [YIING99](https://github.com/YIING99)
