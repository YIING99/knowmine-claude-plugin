---
name: conversation-distill
description: "At the natural end of a conversation, proactively suggest a structured wrap-up (distill): scan the full conversation, classify outputs into 6 categories (insights/decisions/facts/self-observations/action items/open questions), get user confirmation, then batch-write to KnowMine. Complements real-time capture — real-time handles individual highlights, this skill handles session-level holistic distillation. Trigger when: (1) user says closing phrases like 'that's all for now', 'got it', 'thanks', 'wrap up'; (2) 3+ consecutive turns with no new topics; (3) user explicitly says 'distill', 'wrap up', 'save this session'. Do NOT trigger for: quick single-turn Q&A, casual chat, pure coding/debugging tasks with no knowledge output, when user is already actively writing to KnowMine."
---

# Conversation Distill

> The biggest waste of a conversation isn't that nothing was saved — it's that **valuable insights are buried in the process and never revisited**. This skill closes every meaningful conversation with one explicit action: classify → group → confirm → batch-write → surface leftovers.

## When to Use

This skill solves a specific problem: **real-time capture ≠ session-level distillation**.

Real-time capture handles individual highlights as they appear. This skill is the explicit closing ritual — a full scan of the entire conversation to see what was produced, identify relationships, and catch what slipped through.

**Trigger when:**
- User says a closing phrase: "that's all", "got it", "thanks", "done for now", "wrap up"
- 3+ consecutive turns with no new topics (just confirmations or thanks)
- User switches to an unrelated topic, and the previous topic had substantive output not yet saved
- User explicitly invokes: "distill", "save this session", "wrap up", `/knowmine:conversation-distill`

Explicit invocation takes highest priority. Proactive suggestions must be phrased as a **question** — never execute without asking.

**Do NOT trigger for:**
- Quick single-turn queries (one question, one answer)
- Casual conversation or emotional support
- When user is already actively calling write tools
- Pure coding/debugging/execution tasks with no knowledge output
- When user says "don't save" or "skip it"

---

## Five-Step Flow

### Step 1: Full Scan — 6-Category Classification

Scan the entire conversation. Classify everything with distillation value into these 6 categories (skip any category with no content — don't force it):

| Category | Destination | Tagging Rule |
|----------|-------------|--------------|
| 💡 **Insights / Conclusions** | KnowMine — Insights folder | Bilingual tags (EN + native language) |
| 🎯 **Decisions** | KnowMine — relevant folder | Title prefix: `[Decision Log]` |
| 📊 **Facts / Data** | KnowMine — relevant folder | Stable facts: ✅; Time-sensitive data: 🕒 + date |
| 🪞 **Observations about the user** | KnowMine memory / Soul | Use `observe_user_trait` or `save_memory` |
| ✅ **Action items / TODOs** | KnowMine — relevant folder | Tag: `todo:open` (use tags, not new folders) |
| ❓ **Open questions** | KnowMine — relevant folder | Tag: `open-question` |

### Step 2: Relationship Mapping

Find connections between items. Default to **granular over aggregated** (individual entries beat hub documents):

- "These two are different angles on the same decision" → keep as separate entries, cross-reference in body
- "A is the prerequisite for B" → mention A's title or ID in B's body
- "This insight came from this fact" → same approach

**Do not** default to merging everything into one long hub note. Granular entries have higher recall precision in vector search.

### Step 3: User Confirmation (Mandatory)

Present the classification list to the user:

```
This conversation produced N items worth saving:

💡 Insights (3)
  1. {title} → KnowMine / Insights
  2. ...

🎯 Decisions (1)
  4. [Decision Log] {title} → KnowMine / Dev Notes

✅ Action items (2)
  5. {title} → KnowMine / Dev Notes [todo:open]
  ...

Tell me:
- Which to remove? (give numbers)
- Which to edit? (number + edit)
- Which to merge? (numbers)
- Say "write" when ready
```

**Iron rule: do not call any write tool until the user explicitly says "write" or equivalent.** Even "looks good" is not enough — ask once more.

### Step 4: Batch Write

After confirmation, write entries sequentially, reporting back the ID for each. List any failures separately and let the user decide: retry / rewrite / skip.

Follow KnowMine's title format, field conventions, and tagging rules when writing. This skill handles orchestration, not the write rules themselves.

### Step 5: Surface Leftovers

Some conversation content **isn't worth saving to KnowMine but the user might want to keep** (a prompt idea to try next time, a quick reminder, a half-formed thought). Don't force these into KnowMine. Output them as a Markdown block:

```markdown
## Leftovers (not saved — for your reference)

- Next time try this angle with X: ...
- Reminder: ...
- Rough idea (not ready to save): ...
```

---

## Key Principles

- **Granular over hub**: default to separate entries; build hub notes only when needed, with cross-references
- **Confirm before write**: never batch-write without explicit user approval
- **Tags over folders for TODOs**: use `todo:open` tag, don't create a "TODO folder"
- **Time-sensitivity matters**: flag data that will become stale with 🕒 + date
- **Bilingual tags**: helps cross-language vector search recall

---

## How This Differs from Real-Time Capture

| | Real-time capture | Conversation Distill |
|---|---|---|
| When | During conversation, on highlights | At natural conversation end |
| Granularity | Single entry | Entire session |
| Relationship mapping | No | Yes |
| Miss-detection | No | Yes (catches what slipped through) |
| User confirmation | Quick single-entry | Full list review |

Both run in parallel. Real-time capture handles **obvious highlights**. This skill handles **value that's only visible with a full-session view** — relationships, gaps, inconsistent categorization.

---

## Anti-Patterns

- ❌ Batch-writing before user confirms
- ❌ Creating a "TODO folder" (use tags instead)
- ❌ Merging everything into one hub note by default
- ❌ Triggering on short single-turn Q&A
- ❌ Triggering again after user said "don't save"
- ❌ Forcing low-value leftovers into KnowMine

---

## Self-Check Before Step 3

Before presenting the list to the user, verify:

- [ ] Any categories with no real content? (remove them — don't pad)
- [ ] Every decision entry has `[Decision Log]` prefix?
- [ ] Time-sensitive data marked with 🕒 + date?
- [ ] Action items use tag `todo:open`, not a new folder?
- [ ] Any "fake hub" entries that should be split into granular ones?

---

*Part of the KnowMine skill ecosystem. Works alongside `add_knowledge`, `save_memory`, and `recall_memory` MCP tools.*
