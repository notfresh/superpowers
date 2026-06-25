---
name: done-sink
description: Use when a task or workflow completes and you want to reflect on what was discovered, learned, and accomplished — saves knowledge to docs/superpowers/done-sink/ for future reference
---

# Done Sink — Project Knowledge Reflection

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

**Language Detection:** Detect the user's language from their message and respond in the same language. Use the user's own words for confirmation phrases — mirror their style.

Ask in the user's language (e.g., "需要帮你做个回顾吗？" / "Need a reflection?" / "Besoin d'un bilan?" / "需要做个回顾吗？")

## The Process

### Step 1: Offer Reflection

After reporting task completion, detect user's language and offer in that language:

**English:** "Task complete. Want to take a minute to reflect on what we learned?"

**中文:** "任务完成。要不要花一两分钟做个回顾，把这次学到的记下来？"

**Français:** "Tâche terminée. Voulez-vous prendre un moment pour réfléchir à ce qu'on a appris?"

**Deutsch:** "Aufgabe erledigt. Möchten Sie sich eine Minute Zeit nehmen, um zu reflektieren, was wir gelernt haben?"

**日本語:** "タスク完了。这次学んだことを振り返る時間を取りますか？"

*(Detect from user's message and respond in their language — the above are examples. Mirror the user's own phrasing.)*

### Step 2: Handle Response

**If user gives ANY positive confirmation** (e.g., "好" / "是的" / "可以" / "不错" / "很好" / "没问题" / "可以了" / "行" / "yes" / "yeah" / "sure" / "oui" / "ja" / etc.):
- Proceed to Step 3

**If user declines** (e.g., "不用了" / "跳过" / "算了" / "不用" / "no" / "nope" / "skip" / "non" / "nein" / etc.):
- End gracefully in their language: "好的，随时想回顾的时候叫我就行" / "Alright, just let me know if you want to reflect later." / "OK, n'hésitez pas à me le demander plus tard."
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

Show draft to user in their language:

**English:** "Here's a draft — feel free to edit before I save:"

**中文:** "初稿如下，你可以改改再保存："

**Français:** "Voici une première version — n'hésitez pas à modifier avant que je sauvegarde :"

*(Use the user's language. Show the generated draft content below.)*

### Step 4: Save Document

Get confirmed content from user (may have edits).

Generate filename: `docs/superpowers/done-sink/YYYY-MM-DD-<slugified-task-name>.md`

**Slugify rule:** lowercase, spaces to hyphens, remove special chars.

```bash
mkdir -p docs/superpowers/done-sink
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

Confirm to user in their language:

**English:** "Saved. You can find it later here: docs/superpowers/done-sink/<filename>.md"

**中文:** "已保存。以后想回顾项目知识的时候，可以来这个目录看看：docs/superpowers/done-sink/<filename>.md"

**Français:** "Sauvegardé. Vous pourrez le retrouver ici : docs/superpowers/done-sink/<filename>.md"

**Deutsch:** "Gespeichert. Sie finden es später hier: docs/superpowers/done-sink/<filename>.md"

**日本語:** "保存しました。あとで見返す場合はこちら：docs/superpowers/done-sink/<filename>.md"

## Integration Points

This skill should be referenced/invoked from:
- `finishing-a-development-branch` — after presenting options, before cleanup
- `requesting-code-review` — after completing a review cycle
- `executing-plans` — after all tasks complete
- `subagent-driven-development` — after all tasks complete

Integration pattern:
```
After [workflow step], before presenting completion options:
If user gives ANY positive feedback after task completion ("好", "不错", "可以了", "行", "yes", "yeah", "sure", "oui", etc.):
  Ask in their language: (detect and mirror)
  If user agrees:
    Announce: "I'm using the done-sink skill to create a reflection"
    Follow done-sink process (all output in user's language)
```

## Manual Trigger

Natural language trigger is always available — detect the user's language and respond accordingly:

**English:** "create a reflection" / "reflect on this" / "log what we learned"

**中文:** "帮做个回顾" / "记录一下这次学到的" / "做个反思"

**Français:** "créer une réflexion" / "bilan de cette session"

**Deutsch:** "eine Reflexion erstellen" / "festhalten was wir gelernt haben"

**日本語:** "振り返りを作成" / "这次学んだことを記録"

Slash commands (`/reflect`, `/done-sink`) require Claude Code user-level configuration and are not provided by this project.

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