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