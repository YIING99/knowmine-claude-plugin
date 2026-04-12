---
name: conversation-distill
description: "At the natural end of a meaningful conversation, show a one-line soft reminder asking the user if they want to distill — do NOT auto-start. Only ask when the conversation has distillation value (decisions, insights, lessons, open questions, action items). If user says yes, run the full 5-step classify→confirm→write flow. Trigger reminder when: (1) user says closing phrases like 'that's all', 'thanks', 'done', '好的就这样', '没了', '谢谢'; (2) 3+ turns with no new topics AND conversation had substantive content. Do NOT remind for: casual chat, pure Q&A, pure coding/debugging with no decisions, when user already wrote to KnowMine this session."
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
- When user is already actively calling KnowMine write tools this session
- When user says "don't save" or "skip it"

---

## Soft Ask Pattern（先问再做）

检测到结束信号后，**先问一句，不要直接启动 5 步流程**。

### 触发判断（两个条件同时满足才提醒）

**条件 A — 结束信号**（满足其一）：
- 用户说：「谢谢」「好的就这样」「搞定了」「完成了」「没了」「就这些」「thanks」「done」「that's all」「good」
- 连续 3 轮以上无新话题，对话自然收尾

**条件 B — 对话有实质内容**（满足其一）：
- 出现了决策或方案选择
- 讨论了架构、设计、策略
- 产生了值得复用的经验教训或踩坑记录
- 有未记录的 TODO 或开放问题

> 两个条件缺一不可。纯闲聊说「谢谢」不提醒；有实质内容但对话还在进行中不提醒。

### 提醒前：做一次轻量预扫描

出提醒之前，先扫描对话，找出「最有代表性的一条」作为预告。预告要具体——不是「有内容」，而是「有决策 / 有踩坑 / 有 TODO」加一句简短描述。

### 提醒话术（带具体预告）

中文对话：
> 💾 这次对话有 {N} 条值得沉淀的内容（比如{最代表性的一条，10字内}...）。要收尾整理吗？（说「要」开始，「不用」跳过）

英文对话：
> 💾 Found {N} things worth saving from this conversation ({one-line preview, e.g. "the decision about X"}...). Quick distill? (say "yes" to start, "skip" to pass)

**示例：**
- ✅ `💾 这次对话有 3 条值得沉淀的内容（比如你那个关于 MCP 权限分级的设计决策...）。要收尾整理吗？`
- ❌ `💾 这次对话有些值得沉淀的内容，要收尾整理一下吗？`（太泛，不体现理解）

### 用户响应规则

| 用户回复 | Claude 行为 |
|---------|------------|
| 「要」「好」「是」「收尾」「沉淀」「distill」「yes」「go」「start」 | 立即启动完整 5 步流程 |
| 「不用」「算了」「跳过」「skip」「no」「nope」「pass」 | 一句「好的，跳过。」然后停止，**本次对话不再重复提醒** |
| 用户无回应，直接继续新话题 | **不视为永久跳过**——下次出现结束信号时，可以再提醒一次 |

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
