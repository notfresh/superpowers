# Project Knowledge Sync — Design Spec

## Overview

After any task or workflow completes and the AI reports its results, the AI asks the user whether they'd like to do a reflection回顾. If yes, the AI auto-generates initial content based on the conversation, which the user can then edit and supplement before saving as a project knowledge document.

**Goal:** Build a growing project knowledge base over time — discovered structure, learned conventions, and accomplishments — so future work becomes faster as the map gets clearer.

## Triggering

### When to Ask

After any of these events, the AI should ask: "需不需要回顾一下？" (Need a reflection?)

- A task completes in `subagent-driven-development` or `executing-plans`
- A full workflow completes (e.g., after `finishing-a-development-branch`)
- Any time the AI finishes reporting significant work done

### Trigger Modes

All three modes are supported:

1. **A — Confirmation word detection:** When the user says things like "不错"、"很好"、"做得好" after the AI reports work, the AI recognizes this as a positive confirmation and asks about reflection.
2. **B — Manual command:** User can trigger manually with a command like `/reflect` or `/review`.
3. **C — Automatic:** The AI asks proactively after any significant task completion, without requiring a specific trigger word.

Mode A and C are the default behavior. Mode B is always available.

## Content Structure

Each reflection document contains three sections:

```markdown
# <Task Name> (<YYYY-MM-DD>)

## 发现 (What we discovered)
- Project structure and layout
- Technology stack and key files
- Important patterns discovered during work

## 学到 (What we learned)
- Project conventions and coding styles
- Gotchas and pitfalls encountered
- Special approaches specific to this project

## 完成 (What we accomplished)
- What was completed/solved
- Key decisions made
- Results achieved
```

## Flow

1. **AI reports completion** of a task or workflow
2. **AI asks:** "需不需要回顾一下？" (Need a reflection?)
3. **User responds:**
   - "好" / "是的" → proceed to step 4
   - "不用了" / "跳过" → no reflection, end
   - Any other response → interpret as task name for the document
4. **AI auto-generates** content for all three sections based on conversation context
5. **AI shows draft** to user and asks: "以上是我初步总结的，你可以补充修改"
6. **User edits/supplements** the content
7. **AI saves** to `docs/superpowers/done-sync/YYYY-MM-DD-<task-name>.md`
8. **Confirmation** to user

## File Organization

```
docs/superpowers/done-sync/
  2026-05-21-feature-xyz.md
  2026-05-22-bug-fix-auth.md
  2026-05-23-refactor-api.md
```

- One file per task/session
- Filename format: `YYYY-MM-DD-<slugified-task-name>.md`
- Sorted by date, newest first

## Implementation Location

This feature should be integrated into the `finishing-a-development-branch` skill as a new option/branch, and also referenced from `requesting-code-review` and `executing-plans` as a universal post-completion prompt.

## Future Use (Out of Scope for V1)

- Automatic context loading from knowledge base at session start (Mode A from the request)
- Queryable knowledge base with `/knowledge` command
- Project map visualization
- Skill auto-discovery based on learned conventions

These are noted for future extension but not implemented in V1.

## File Format

All documents use UTF-8 encoding with Chinese headings as specified. The structure is intentionally flat to make manual editing easy and future parsing simple.