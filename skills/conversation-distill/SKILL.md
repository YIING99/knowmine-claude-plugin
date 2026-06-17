---
name: conversation-distill
description: "Use when a user asks to distill/save a conversation to KnowMine, says 沉淀/收尾/归档本次对话, or a meaningful conversation naturally ends with reusable decisions, insights, lessons, module patterns, business judgment, open questions, or durable preferences. Do not use for routine execution logs, raw transcripts, IDs, tool output, local handoff details, or content already preserved in project docs."
---

# Conversation Distill (KnowMine Edition)

Conversation distillation is not a transcript summary and not a category sorter. Its job is to preserve the small amount of knowledge that will make a future conversation smarter.

Default stance: fewer, sharper notes. If an item will not help future decisions, reuse, diagnosis, product/module design, or user understanding, leave it out.

## Mode Routing

### Soft Reminder Mode

Use this only at a natural ending, and only when both conditions are true:

- Ending signal: "谢谢", "好的就这样", "搞定了", "完成了", "没了", "done", "that's all", topic closed, or 3+ turns with no new topic.
- Reuse signal: the conversation produced a decision, durable insight, reusable workflow, non-obvious pitfall, module pattern, business judgment, open question, or stable user preference.

Ask one line. Do not start distilling yet.

Chinese:
> 这次有 {N} 条可能值得沉淀的内容（比如{10字内具体预告}）。要我收成可复用笔记吗？

English:
> Found {N} potentially reusable takeaways from this conversation ({short preview}). Distill them?

If the user says skip/no/不用, acknowledge once and do not remind again in this conversation.

### Explicit Distill Mode

If the user says "沉淀", "收尾整理", "写入 KnowMine", "distill this session", or equivalent, start the value-first flow immediately. Still do not write until the user explicitly confirms.

## Value-First Flow

### Step 1: Reuse-Value Gate

Scan the conversation and keep an item only if it passes at least one gate:

| Gate | Keep if the item captures... |
|---|---|
| Module pattern | A reusable product/business/process module, design pattern, operating model, or decision logic |
| Durable insight | A lesson, mental model, causality, tradeoff, or principle likely useful in other work |
| Business judgment | Evidence chain + judgment + action + result/feedback, especially customer follow-up, quote, negotiation, or management decisions |
| Stable preference | A user preference or working style that should change future agent behavior |
| Non-obvious pitfall | A failure mode, gotcha, boundary, or correction that prevents repeated mistakes |
| Future lever | An unresolved strategic question or action that materially affects future work |

Reject items whose main value is local bookkeeping:

- Raw transcript summaries or "what happened in this chat"
- Exact IDs, table names, tokens, local paths, file lists, command lists, tool output, status receipts
- TODOs already preserved in a handoff doc, PR, README, issue, changelog, or project-local file
- One-off implementation facts that future agents can read from code or docs
- Obvious restatements of the user's request
- Sensitive customer/business details unless the user explicitly asks to persist them remotely

If the conversation is mostly routine execution and nothing passes the gate, say so and recommend no KnowMine write.

### Step 2: Distill Into 2-5 Candidates

Prefer 2-5 high-signal candidates over exhaustive lists. Each candidate should be a reusable note, not a category label.

Use this compact shape:

```markdown
我建议只沉淀这 {N} 条：

1. {type} - 《{title}》
   以后价值：{why this is reusable}
   内容：{3-6 bullets or one tight paragraph}
   建议写入：{KnowMine add_knowledge/save_memory/observe_user_trait/local only}

不写入的内容：{briefly name excluded bookkeeping items}

回「写入」我再保存；要删/改/合并也可以直接说编号。
```

Types are descriptive, not mandatory buckets:

- `洞察`
- `决策`
- `模块信息`
- `判断卡`
- `偏好`
- `踩坑/边界`
- `开放问题`

Do not force empty types. Do not reproduce the old six-category checklist unless the user explicitly asks for a full audit.

For `判断卡`, use it only when there is a falsifiable business judgment, not a vague opinion. Keep the card compact:

```markdown
判断：{one falsifiable sentence}
依据：{source/evidence chain, with uncertainty if needed}
因果链：{why this should lead to that result}
行动/结果：{what was done or what feedback will verify it}
可复用经验：{what future work should remember}
状态：未验证 / 已验证 / 已推翻
```

### Step 3: Relationship Mapping

Add relationship notes only when they improve future retrieval:

- `extends`: this note extends a known background package, decision, or prior KnowMine entry.
- `evolved_from`: this updates or corrects an older decision.
- `source`: point to the local project doc or handoff file when detailed evidence belongs there.

Do not create a hub note merely to connect everything. Semantic search works better with small, standalone reusable notes.

### Step 4: User Confirmation

Before any write, ask for confirmation. The user can remove, edit, merge, or approve candidates.

Accept confirmations such as "写入", "全部写", "OK 存", "save", "write".

Iron rule: do not call KnowMine write tools before explicit confirmation.

### Step 5: Write and Verify

When confirmed:

1. Route the target correctly:
   - Reusable cross-project knowledge -> KnowMine `add_knowledge`
   - Stable user preference -> `save_memory` or `observe_user_trait`
   - Project-specific evidence -> local project docs, not KnowMine, unless explicitly requested
2. Search/list first if duplicate risk is obvious.
3. Write sequentially.
4. Report each saved ID.
5. Verify with read/search when available. If verification fails, report the uncertainty instead of claiming completion.

## Special Guidance For Business / Module Conversations

When the conversation involves sales ops, customer management, Feishu/CRM modules, quote workflows, or team operating systems, the valuable layer is usually the reusable module logic:

- What operating model emerged?
- What design principle or management insight will transfer to future projects?
- What old decision changed and why?
- Which constraints are guardrails, not mere implementation detail?
- What should a future agent remember before designing a similar module?

Exclude project ledger details unless they are the actual reusable lesson. For example, a Base ID or CLI subcommand belongs in a handoff doc; "thin follow-up log + commander dashboard + hidden scoring as an operating pattern" may belong in KnowMine.

## Real Failure To Avoid

Bad distillation shape:

- 10 items across insights / decisions / facts / user observation / TODO / open question
- Includes Base/Table IDs, command availability, next script TODO, and other handoff material
- Explains where each item should be filed, but not why it matters later

Better shape:

- 2-3 reusable candidates
- Each candidate is a module insight, decision logic, judgment card, or durable preference
- Local evidence and TODOs stay in the handoff doc
- User confirms before write

## Self-Check Before Showing Candidates

- [ ] Does every candidate have an explicit "以后价值" reason?
- [ ] Did I remove project ledger details that already live in local docs?
- [ ] Is the list short enough that the user can approve it quickly?
- [ ] Am I preserving reusable insight rather than summarizing the chat?
- [ ] Am I waiting for the user's "写入" before calling tools?

## Standalone Version

This KnowMine edition is part of the [knowmine-claude-plugin](https://github.com/YIING99/knowmine-claude-plugin).

A tool-agnostic version is available separately:
→ [github.com/YIING99/conversation-distill](https://github.com/YIING99/conversation-distill)
→ `npx clawhub@latest install conversation-distill`
