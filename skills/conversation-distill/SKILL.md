---
name: conversation-distill
description: "At the natural end of a conversation, proactively suggest a structured wrap-up (distill): scan the full session, classify outputs into 6 categories (insights/decisions/facts/self-observations/action-items/open-questions), get explicit user confirmation, then batch-write to KnowMine via MCP. Trigger when: (1) user says closing phrases like 'that's all', 'got it', 'thanks', 'wrap up'; (2) 3+ consecutive turns with no new topics; (3) user explicitly says 'distill', 'wrap up', 'save this session', '收尾', '沉淀'. Do NOT trigger for: single-turn Q&A, casual chat, pure coding/debugging with no knowledge output, when user is already actively writing to KnowMine."
---

# Conversation Distill (KnowMine Edition)

> The biggest waste of a conversation isn't that nothing was saved — it's that **valuable insights are buried in the process and never revisited**.
>
> This skill closes every meaningful conversation with one explicit action: **classify → confirm → write to KnowMine**.

## When to Use

**The core problem**: real-time capture ≠ session-level distillation.

Real-time capture handles individual highlights. This skill is the closing ritual — a full scan of the session to see what was produced, map relationships, and catch what slipped through.

**Trigger when:**
- User says a closing phrase: "that's all", "got it", "thanks", "done for now", "wrap up", "收尾", "好的就这样"
- 3+ consecutive turns with no new topics
- User switches to an unrelated topic; previous topic had substantive output not yet saved
- User explicitly invokes: "distill", "save this session", "wrap up", "沉淀一下", `/knowmine:conversation-distill`

**Do NOT trigger for:**
- Quick single-turn queries
- Casual conversation or emotional support
- Pure coding/debugging/execution tasks with no knowledge output
- When user is already actively calling KnowMine write tools
- When user says "don't save" or "skip it"

---

## Five-Step Flow

### Step 1: Full Scan — 6-Category Classification

Scan the entire conversation. Classify everything with distillation value. **Skip any empty category — don't force it.**

| Category | KnowMine Tool | Tagging Rule |
|----------|--------------|--------------|
| 💡 **Insights / Conclusions** | `add_knowledge` | type: `insight`; bilingual tags |
| 🎯 **Decisions** | `add_knowledge` | type: `note`; title prefix `[Decision Log]` |
| 📊 **Facts / Data** | `add_knowledge` | type: `reference`; stable: ✅, time-sensitive: 🕒 + date |
| 🪞 **Observations about the user** | `observe_user_trait` or `save_memory` | Use `save_memory` for preferences/habits; `observe_user_trait` for inferred traits |
| ✅ **Action items / TODOs** | `add_knowledge` | type: `note`; tag `todo:open`; NO new folder — use tag |
| ❓ **Open questions** | `add_knowledge` | type: `note`; tag `open-question` |

### Step 2: Relationship Mapping

Find connections. Default to **granular over aggregated**:

- Same decision, different angles → separate entries, cross-reference in body
- A is prerequisite for B → mention A's title/ID in B's body
- Insight came from a fact → note the source

**Do not** merge into one hub document. Granular entries have higher vector search precision.

### Step 3: User Confirmation (Mandatory)

Present the list:

```
This conversation produced N items worth saving to KnowMine:

💡 Insights (2)
  1. {title} → add_knowledge [insight]
  2. {title} → add_knowledge [insight]

🎯 Decisions (1)
  3. [Decision Log] {title} → add_knowledge [note]

✅ Action items (2)
  4. {title} → add_knowledge [note] #todo:open
  5. {title} → add_knowledge [note] #todo:open

Tell me:
- Numbers to remove
- Numbers to edit (number + new version)
- Numbers to merge
- Say "write" when ready
```

**Iron rule: do not call any KnowMine tool until the user explicitly says "write" or equivalent.**

### Step 4: Batch Write to KnowMine

After confirmation, write entries sequentially. Report back the KnowMine ID for each success. List failures separately — user decides: retry / rewrite / skip.

**Tool mapping:**
- Insights, decisions, facts, action items, open questions → `add_knowledge`
- User preferences, habits, self-observations → `save_memory` (type: `preference`) or `observe_user_trait`

**Title format:** `[Decision Log] {title}` for decisions; plain title for everything else.

**Tags:** always include at least one English tag. Add native language tag if the content is in another language.

### Step 5: Surface Leftovers

Output anything not worth saving as plain Markdown:

```markdown
## Leftovers (not saved)

- [rough idea or reminder]
- [something to try next time]
```

---

## Key Principles

- **Granular over hub** — one insight per entry beats one long summary
- **Confirm before write** — mandatory, no exceptions
- **Tags over folders for TODOs** — `todo:open` tag, not a new folder
- **Bilingual tags** — EN + native language improves cross-language recall
- **Time-sensitivity** — flag stale-prone data with 🕒 + date

---

## Self-Check Before Step 3

- [ ] Any empty categories removed?
- [ ] Every decision has `[Decision Log]` prefix?
- [ ] Time-sensitive data marked 🕒 + date?
- [ ] Action items tagged `todo:open`, not put in a new folder?
- [ ] Any "fake hub" entries that should be split?

---

## Standalone Version

This KnowMine edition is part of the [knowmine-claude-plugin](https://github.com/YIING99/knowmine-claude-plugin).

A tool-agnostic version (works with any notes tool or plain Markdown output) is available separately:
→ [github.com/YIING99/conversation-distill](https://github.com/YIING99/conversation-distill)
→ `npx clawhub@latest install conversation-distill`
