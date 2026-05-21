# Project Knowledge Sync Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a "done-sync" skill that prompts for reflection after task completion and saves knowledge documents to `docs/superpowers/done-sync/`.

**Architecture:** Create a new `done-sync` skill that integrates with existing workflow skills. Trigger reflection after task completion in multiple skills. Auto-generate content from conversation context, save as dated markdown files.

**Tech Stack:** Skill files (markdown), no external dependencies.

---

## Task 1: Create done-sync Skill Skeleton

**Files:**
- Create: `skills/done-sync/SKILL.md`

- [ ] **Step 1: Create directory and SKILL.md file**

```markdown
---
name: done-sync
description: Use when a task or workflow completes and you want to reflect on what was discovered, learned, and accomplished — saves knowledge to docs/superpowers/done-sync/ for future reference
---

# Done Sync — Project Knowledge Reflection

## Overview

After completing work, offer to create a reflection document that captures:
- What we discovered (project structure, tech stack, key files)
- What we learned (conventions, gotchas, project-specific approaches)
- What we accomplished (completed work, solutions, decisions)

This builds a growing project knowledge base over time.

## When to Trigger

Offer reflection after ANY of these:
- A task completes in subagent-driven-development
- A full workflow completes (finishing-a-development-branch)
- Any significant work completion where results were reported

Ask: "需不需要回顾一下？" (Need a reflection?)

## The Process

### Step 1: Offer Reflection

After reporting task completion:
```
"任务完成。以上就是这次的工作成果。需不需要回顾一下？"
```

Wait for user response.

### Step 2: Handle Response

**If user agrees ("好" / "是的" / "需要"):**
- Proceed to Step 3

**If user declines ("不用了" / "跳过" / "算了"):**
- End gracefully: "好的，以后想回顾的时候随时告诉我"
- Stop

**If user provides a name:**
- Use as task name for the document
- Proceed to Step 3

### Step 3: Auto-Generate Content

Based on conversation context, generate draft content:

```markdown
# <Task Name> (<YYYY-MM-DD>)

## 发现 (What we discovered)
- (auto-generated based on conversation)

## 学到 (What we learned)
- (auto-generated based on conversation)

## 完成 (What we accomplished)
- (auto-generated based on conversation)
```

Show draft to user:
```
以上是我初步总结的，你可以补充修改：

<draft content>

---
确认保存？还是需要调整？
```

### Step 4: Save Document

Get confirmed content from user (may have edits).

Generate filename: `docs/superpowers/done-sync/YYYY-MM-DD-<slugified-task-name>.md`

**Slugify rule:** lowercase, spaces to hyphens, remove special chars.

```bash
mkdir -p docs/superpowers/done-sync
```

Save file:
```markdown
# <Task Name> (<YYYY-MM-DD>)

## 发现 (What we discovered)
- ...

## 学到 (What we learned)
- ...

## 完成 (What we accomplished)
- ...
```

Confirm to user:
```
已保存到 docs/superpowers/done-sync/<filename>.md
以后想回顾项目知识的时候，可以查阅这个目录
```

## Integration Points

This skill should be referenced/invoked from:
- `finishing-a-development-branch` — after presenting options, before cleanup
- `requesting-code-review` — after completing a review cycle
- `executing-plans` — after all tasks complete
- `subagent-driven-development` — after all tasks complete

Integration pattern:
```
After [workflow step], before presenting completion options:
If user expresses satisfaction ("不错", "很好", etc.) or after reporting significant work:
  Announce: "I'm using the done-sync skill to create a reflection"
  Follow done-sync process
```

## Manual Trigger

User can also trigger manually with `/reflect` or `/done-sync`.

## File Format

- Encoding: UTF-8
- Line endings: LF
- Structure: Three sections as shown
- Slugification: `date + task-name`, lowercase, hyphens instead of spaces

## Red Flags

**Never:**
- Save empty content (at least one bullet per section)
- Overwrite existing files with same name (use unique filename)
- Skip the review/edit step (user must confirm content)
- Offer reflection in the middle of work (only after completion)

**Always:**
- Save to correct directory
- Confirm with user before saving
- Use today's date in filename
```

- [ ] **Step 2: Commit**

```bash
git add skills/done-sync/SKILL.md
git commit -m "feat: add done-sync skill for project knowledge reflection"
```

---

## Task 2: Integrate done-sync into finishing-a-development-branch

**Files:**
- Modify: `skills/finishing-a-development-branch/SKILL.md`

- [ ] **Step 1: Read current skill**

Read `skills/finishing-a-development-branch/SKILL.md` to understand current structure.

- [ ] **Step 2: Add done-sync trigger before Step 1**

After "Announce at start" and before "Verify Tests", add:

```
### Step 0: Offer Reflection

After announcing completion but before verifying tests:

Ask: "任务完成，需不需要回顾一下？"

If user agrees:
  Announce: "I'm using the done-sync skill"
  Use superpowers:done-sync
  Follow done-sync process

If user declines or after done-sync completes:
  Continue to Step 1
```

- [ ] **Step 3: Commit**

```bash
git add skills/finishing-a-development-branch/SKILL.md
git commit -m "enhance: trigger done-sync reflection before cleanup"
```

---

## Task 3: Integrate done-sync into requesting-code-review

**Files:**
- Modify: `skills/requesting-code-review/SKILL.md`

- [ ] **Step 1: Read current skill**

Read `skills/requesting-code-review/SKILL.md`.

- [ ] **Step 2: Add done-sync trigger after review cycle**

In "How to Request", after "Act on feedback" section, add:

```
### After All Reviews

After completing review and before returning to normal work:

If work was significant (fixed issues / completed feature):
  Ask: "需不需要回顾一下，把这次的发现/学到/完成的内容沉淀下来？"

  If user agrees:
    Announce: "I'm using the done-sync skill"
    Use superpowers:done-sync
    Follow done-sync process
```

- [ ] **Step 3: Commit**

```bash
git add skills/requesting-code-review/SKILL.md
git commit -m "enhance: trigger done-sync after code review cycle"
```

---

## Task 4: Integrate done-sync into executing-plans

**Files:**
- Modify: `skills/executing-plans/SKILL.md`

- [ ] **Step 1: Read current skill**

Read `skills/executing-plans/SKILL.md`.

- [ ] **Step 2: Add done-sync trigger in Step 3**

In "Step 3: Complete Development", after invoking `finishing-a-development-branch`, add:

```
### After finishing-a-development-branch completes:

If user chose Option 2 (Create PR) or Option 3 (Keep as-is) and was satisfied:
  Ask: "需不需要回顾一下？"

  If user agrees:
    Announce: "I'm using the done-sync skill"
    Use superpowers:done-sync
    Follow done-sync process
```

- [ ] **Step 3: Commit**

```bash
git add skills/executing-plans/SKILL.md
git commit -m "enhance: trigger done-sync after plan execution"
```

---

## Task 5: Integrate done-sync into subagent-driven-development

**Files:**
- Modify: `skills/subagent-driven-development/SKILL.md`

- [ ] **Step 1: Read current skill**

Read `skills/subagent-driven-development/SKILL.md`.

- [ ] **Step 2: Add done-sync trigger after all tasks complete**

In the final step after "Dispatch final code reviewer subagent" and before "Use superpowers:finishing-a-development-branch", add:

```
### Before finishing-a-development-branch:

After final reviewer approves and before offering completion options:
  Ask: "所有任务完成，需不需要回顾一下？"

  If user agrees:
    Announce: "I'm using the done-sync skill"
    Use superpowers:done-sync
    Follow done-sync process
```

- [ ] **Step 3: Commit**

```bash
git add skills/subagent-driven-development/SKILL.md
git commit -m "enhance: trigger done-sync after subagent-driven tasks complete"
```

---

## Task 6: Create example done-sync output file

**Files:**
- Create: `docs/superpowers/done-sync/2026-05-21-example-reflection.md`

- [ ] **Step 1: Create example file with placeholder content**

```markdown
# 示例任务名 (2026-05-21)

## 发现 (What we discovered)
- 项目采用 monorepo 结构
- 主要技术栈: Go + TypeScript
- 关键文件: pkg/, internal/, skills/

## 学到 (What we learned)
- 技能文件使用 YAML frontmatter
- 遵循特定的文件命名和目录结构
- 测试需要使用特定 tags 和 flags

## 完成 (What we accomplished)
- 创建了 done-sync 技能
- 集成了项目知识沉淀功能
- 建立了 docs/superpowers/done-sync/ 目录结构
```

- [ ] **Step 2: Commit**

```bash
git add docs/superpowers/done-sync/2026-05-21-example-reflection.md
git commit -m "docs: add example done-sync output file"
```

---

## Spec Coverage Check

| Spec Requirement | Task |
|------------------|------|
| Trigger after task completion | Tasks 2-5 (integration) |
| Ask "需不需要回顾一下？" | Task 1 (done-sync skill) |
| Auto-generate content | Task 1 (done-sync skill) |
| User edits/supplements | Task 1 (done-sync skill) |
| Save to done-sync directory | Task 1 (done-sync skill) |
| File format with date + task name | Task 1 (done-sync skill) |
| Integration into workflow skills | Tasks 2-5 |

**Gaps:** None identified.

## Self-Review

1. **Placeholder scan:** No TBD/TODO placeholders in plan.
2. **Type consistency:** Skill names are consistent throughout.
3. **Spec coverage:** All spec requirements mapped to tasks.