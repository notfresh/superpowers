---
name: explore
description: Use when you want to understand an unfamiliar codebase or project area — explores code architecture, conventions, and design through dialogue, and sediments findings to docs/superpowers/explorations/ as a growing project knowledge base.
---

# Explore — Project Codebase Investigation

## Overview

This skill lets an agent explore a project's code architecture, conventions, and design through natural dialogue, and sediment the findings as Markdown files that grow into a reusable knowledge base over time.

Two sediment files are produced and maintained:

- `INDEX.md` — dictionary of **explored topics** (agent-generated knowledge)
- `DOCUMENTS.md` — registry of **discovered project documents** (existing files) with value ratings

The skill is intentionally minimal: it uses only built-in Read/Write/Edit tools, no scripts, no hooks, no harness config changes.

## Goals

- Make codebase exploration **fast and conversational** — `/explore auth` returns a usable explanation in one turn.
- Build a **growing knowledge base** that survives sessions via the repository's own `docs/` directory.
- Be a **resource** that other skills can opt into (`Read INDEX.md` / `Read DOCUMENTS.md`) without code changes.
- Keep change scope **small** — five new files, zero edits to existing files.

## Non-Goals (YAGNI)

- Cross-harness support beyond Claude Code in this skill.
- Slash command auto-registration in any harness manifest.
- A script/tool for managing the sediment files (Read/Write/Edit are enough).
- Schema validation, linting, or CI checks for `INDEX.md` / `DOCUMENTS.md`.
- Automatic archival, rename, or deletion of 🟠 "needs organizing" documents.
- Modifying any other skill to use these resources (we document the integration; we don't enforce it).

## Triggers

The skill activates on any of:

- **Explicit slash command:** `/explore` or `/explore <topic>`
- **Natural language:** "探索一下 X"、"调研一下"、"看看这个项目怎么做的"、"explore X"
- **Implicit proposal:** The agent recognizes the user is unclear about a part of the project that `INDEX.md` does not cover, and proposes "要不要我探索一下 X?"

The agent must announce the invocation: `"Using the explore skill to ..."`.

## Two Modes

### Mode 1 — Directed

The user supplied a topic (via slash argument, natural language, or in response to a proposal).

Steps:

1. Restate the topic: "好,我要探索 <topic>。"
2. **Read DOCUMENTS.md first.** For each entry, decide relevance by comparing the document's one-line description against the topic. Read only relevant documents. Skip 🟢/🟡 entries whose description is clearly off-topic. Pause on 🟠 entries and ask the user whether to read them.
3. Read the relevant code: `Glob` to locate files, then `Read` 1–3 rounds, targeted by the topic.
4. Form a structured mental model: architecture, key files, flow, gotchas.
5. **Cross-check code vs documents**, but only for documents whose content is being actively used to inform this topic. If a mismatch is found, record it under `## 代码 vs 文档` in the exploration file. Do not chase mismatches that are not relevant to the current topic.
6. Generate `docs/superpowers/explorations/<slug>.md` with the format defined below.
7. Update `docs/superpowers/explorations/INDEX.md` (append one entry under the appropriate category).
8. Update `docs/superpowers/explorations/DOCUMENTS.md` (append one entry per **newly** read document, with a one-line impression and a tier).
9. Report: "已写入 `<path>`,INDEX.md 和 DOCUMENTS.md 已更新。"

### Mode 2 — Casual

The user invoked `/explore` or "探索一下" with no topic.

Steps:

1. Read `INDEX.md`.
2. Branch:
   - **a) INDEX.md missing or empty:** Ask the user: "你最想了解这个项目的哪块?"
   - **b) INDEX.md has content:** Identify categories that exist but are sparse (≤1 entry) and any obviously missing categories. Propose 1–2 candidates: "INDEX.md 里 `<X>` 还是空的,要探索这块吗?或者你心里有别的方向?"
3. Wait for the user's selection.
4. Hand off to Mode 1 with the chosen topic.

The casual mode never picks a topic fully on its own — the user always confirms. This is "agent proposes, user disposes."

## Sediment File Formats

### `INDEX.md` — Explored Topics Dictionary

```markdown
# Project Knowledge Index

> 最后更新: 2026-06-13 · 共 N 项探索

## 架构 (Architecture)
- [认证流](auth-flow.md) — 登录、会话、token 校验的端到端流程
- [构建管线](build-pipeline.md) — 从源码到可部署产物的步骤

## 数据模型 (Data Model)
- [用户表结构](user-schema.md) — 用户核心字段及关联关系

## 约定 (Conventions)
- [错误处理](error-handling.md) — 错误如何传递与暴露
- [测试模式](testing-patterns.md) — 单元、集成、E2E 测试的写法

## 杂项 (Misc)
- [日志实践](logging.md) — 日志分级、格式、采集点

---
*新增探索时,挑已有分类追加,或新建一个分类 section。保持分类少而稳。*
```

- **Categories are human-semantic** (架构 / 数据模型 / 约定 / 杂项), not technical tags.
- **Each entry: title (linking to a single exploration file) + one-line description** — enough to decide whether to open the file.
- The header line (`最后更新 ... 共 N 项`) is rewritten on every update.
- The footer is the only static content; the rest is regenerated.

### `DOCUMENTS.md` — Discovered Documents Registry

```markdown
# Project Documents Registry

> 最后更新: 2026-06-13 · 已识别 8 份文档

## 必读 (🟢)
- `README.md` — 项目目标/快速上手/贡献指南,每次探索都先扫一眼
- `docs/superpowers/specs/` — 既有设计文档,涉及架构必查
- `skills/done-sink/SKILL.md` — 沉淀物模式参考,新文档格式可对齐

## 参考 (🟡)
- `docs/windows/polyglot-hooks.md` — 多平台 hook 差异,仅当改动 hook 时读
- `RELEASE-NOTES.md` — 变更历史,改完一个功能后查是否要更新

## 跳过 (🔴)
- `package.json` — 依赖清单,与代码探索关系不大(已记住:无运行时依赖)

## 待整理 (🟠)
- `docs/superpowers/specs/2026-01-22-document-review-system-design.md` — 内容似乎已废弃,文件还在仓库

---
*描述越准,后续越能判断"是否与当前主题相关"。新增文档时给一句"什么时候该读"。*
```

Four tiers:

| Tier | Symbol | Meaning | Behavior |
|---|---|---|---|
| 必读 | 🟢 | Always relevant in some form | Read or skim on every exploration |
| 参考 | 🟡 | Relevant only in some topics | Read only when description matches the current topic |
| 跳过 | 🔴 | Not useful for exploration | Never read again (recorded so we don't re-Read) |
| 待整理 | 🟠 | Suspicious / stale / misnamed | Pause and ask the user before reading |

The footer reinforces the discipline: descriptions must include "when to read" so future relevance judgments are cheap.

### Single Exploration File — `<slug>.md`

```markdown
# 认证流 (2026-06-13)

> 一句话总结: 基于 JWT,access 15 分钟 / refresh 7 天,Redis 存黑名单。

## 关键文件
- `src/auth/login.ts:42` — 登录入口,签发 token
- `src/auth/middleware.ts:1` — 校验 access token
- `src/auth/refresh.ts:88` — 刷新逻辑

## 流程
1. 用户 POST /login → 校验密码 → 签发 access + refresh
2. 后续请求带 Authorization header
3. 中间件校验签名、过期、黑名单
4. 过期用 refresh 换新 access

## 坑
- `login.ts:42` 不会主动撤销旧 token,用户改密码后旧 token 仍有效 15 分钟
- `refresh.ts:120` 在 Redis 故障时降级为"放行所有 refresh token",安全风险

## 代码 vs 文档
- `README.md` 第 12 行说"使用 cookie session",但 `src/auth/middleware.ts:1` 实际校验 JWT。README 可能过期。

## 关联
- 相关: 用户表结构
- 待探索: 权限控制 (RBAC 是不是这套)
```

- The first line is a one-sentence summary — the dominant scan path is "INDEX.md + first line".
- Key files include `path:line` for direct Read targeting by future agents.
- The `坑` section is highlighted as the highest-value content.
- The `代码 vs 文档` section is **omitted entirely** when no relevant mismatch was found; it is added only when one is found and is relevant to the topic.
- The `关联` section cross-links INDEX.md entries and points to follow-up topics.

## Red Flags (Discipline Rules)

**Never:**

- Save an exploration file with empty content (at least one bullet under `## 关键文件` or `## 流程`).
- Overwrite an existing `<slug>.md` — if a file with that slug exists, append a date suffix (`<slug>-2026-06-13.md`).
- Trigger the casual-mode proposal in the middle of unrelated work.
- Read a 🟢/🟡 document whose one-line description is clearly off-topic for the current topic.
- Read any 🔴 document.
- Read a 🟠 document without first asking the user.
- Report a code-vs-doc mismatch that does not relate to the current topic.
- Delete, rename, or archive a 🟠 document without user confirmation.

**Always:**

- Read `DOCUMENTS.md` before reading any other project document.
- Update `INDEX.md` and `DOCUMENTS.md` together with every new exploration.
- Include the date in the exploration file's H1 heading.
- One-line impression for every newly registered document.
- Use `path:line` for file references inside the exploration file.

## Integration Points

This skill is **purely additive**. No other skill is required to change. The integration is documented here and stands on its own:

- `INDEX.md` and `DOCUMENTS.md` live at `docs/superpowers/explorations/`.
- When any skill needs project context, it can:
  1. Read `INDEX.md` to find related explorations.
  2. Read `DOCUMENTS.md` to find relevant documents (and skip irrelevant ones).
  3. Read specific files only if relevant.
- Skills are NOT required to use this; it's an opt-in resource.
- No hooks, no automatic injection — purely pull-based.
