# Brainstorming Consults Explore Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Modify `skills/brainstorming/SKILL.md` so that brainstorm sessions consult the explore skill's sediment files (`INDEX.md`, `DOCUMENTS.md`) at the start of Step 1 ("Explore project context"). Empty / missing / off-topic → silent skip. Relevant entries → active citation in subsequent steps.

**Architecture:** Three discrete edits to one file (`skills/brainstorming/SKILL.md`): (1) `## Checklist` Step 1 expanded with sub-item 1a, (2) new `## Reading Exploration Context` section added under `## The Process → Understanding the idea`, (3) `## Process Flow` dot graph gains a "Read exploration context" sub-node. Zero new files, zero new skills, zero new hooks.

**Tech Stack:** Markdown (no code, no scripts, no runtime). The "tests" are: diff verification (post-each-task), spec walkthrough (post-implementation), optional real agent smoke test (post-merge).

---

## File Structure

| File | Status | Purpose |
|---|---|---|
| `skills/brainstorming/SKILL.md` | modify | The only file being changed. Hosts all three edits. |
| `docs/superpowers/specs/2026-06-13-brainstorming-consults-explore-design.md` | already committed (commit `ee0a6a3`) | The design spec this plan implements. Read it for context. |

No other files created or modified. The explore skill (`skills/explore/SKILL.md`) is **not** changed.

---

## Task 1: Add 1a sub-item to `## Checklist`

**Files:**
- Modify: `skills/brainstorming/SKILL.md` (the `## Checklist` section, currently around lines 22–32)

- [ ] **Step 1: Read the current `## Checklist` section to confirm exact text**

Run:
```bash
cd "$(git rev-parse --show-toplevel)" && sed -n '20,35p' skills/brainstorming/SKILL.md
```

Expected: the existing 9-item checklist where Step 1 reads `1. **Explore project context** — check files, docs, recent commits` and Step 2 reads `2. **Offer visual companion** (if topic will involve visual questions) — this is its own message, not combined with a clarifying question. See the Visual Companion section below.`

If the wording differs from above (e.g., the file was edited after the spec was written), use the actual text in the next step.

- [ ] **Step 2: Apply the edit using the Edit tool**

Use the Edit tool with:
- `file_path`: `skills/brainstorming/SKILL.md`
- `old_string`:
```
1. **Explore project context** — check files, docs, recent commits
2. **Offer visual companion** (if topic will involve visual questions) — this is its own message, not combined with a clarifying question. See the Visual Companion section below.
```
- `new_string`:
```
1. **Explore project context** — check files, docs, recent commits
   1a. **Read exploration context** — read `docs/superpowers/explorations/INDEX.md` (and `DOCUMENTS.md` if helpful). Empty, missing, or no relevant entries → proceed normally. Relevant entries → Read those files; cite explicitly in subsequent steps.
2. **Offer visual companion** (if topic will involve visual questions) — this is its own message, not combined with a clarifying question. See the Visual Companion section below.
```
- `replace_all`: false

- [ ] **Step 3: Verify the edit applied correctly**

Run:
```bash
sed -n '20,35p' skills/brainstorming/SKILL.md
```

Expected output (exact text):
```
1. **Explore project context** — check files, docs, recent commits
   1a. **Read exploration context** — read `docs/superpowers/explorations/INDEX.md` (and `DOCUMENTS.md` if helpful). Empty, missing, or no relevant entries → proceed normally. Relevant entries → Read those files; cite explicitly in subsequent steps.
2. **Offer visual companion** (if topic will involve visual questions) — this is its own message, not combined with a clarifying question. See the Visual Companion section below.
```

- [ ] **Step 4: Confirm the diff is exactly the expected change**

Run:
```bash
git diff skills/brainstorming/SKILL.md
```

Expected: exactly **1 line added** (the 1a sub-item), **0 lines removed**, no other files in the diff. The added line is indented (3 spaces) under Step 1.

If the diff shows more than 1 line, revert (`git checkout -- skills/brainstorming/SKILL.md`) and re-apply Step 2.

- [ ] **Step 5: Commit**

```bash
git add skills/brainstorming/SKILL.md
git commit -m "$(cat <<'EOF'
feat(brainstorming): add 'Read exploration context' sub-step to Step 1

Step 1 (Explore project context) now has a 1a sub-item instructing the
agent to read the explore skill's sediment files (INDEX.md, optionally
DOCUMENTS.md) at the start of a brainstorm session. Empty / missing /
off-topic files are silently skipped; relevant entries are cited
explicitly in subsequent steps.

Purely text change; ~1 line added, 0 removed. Behavior documented in
the new "Reading Exploration Context" section (added in the next
commit).

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
EOF
)"
```

---

## Task 2: Add `## Reading Exploration Context` section

**Files:**
- Modify: `skills/brainstorming/SKILL.md` (insert a new section under `## The Process → Understanding the idea`)

- [ ] **Step 1: Read the current "Understanding the idea" subsection**

Run:
```bash
cd "$(git rev-parse --show-toplevel)" && sed -n '68,82p' skills/brainstorming/SKILL.md
```

Expected: you should see a paragraph starting with `**Understanding the idea:**` followed by bullets, then the `**Exploring approaches:**` sub-heading. The new section goes **between** these two (after the "Understanding the idea" content, before "Exploring approaches").

If the structure has changed, locate the actual insertion point: the new section must be inside `## The Process`, before the `**Exploring approaches:**` sub-heading.

- [ ] **Step 2: Apply the edit using the Edit tool**

Use the Edit tool with:
- `file_path`: `skills/brainstorming/SKILL.md`
- `old_string`:
```
- If the project is too large for a single spec, help the user decompose into sub-projects: what are the independent pieces, how do they relate, what order should they be built? Then brainstorm the first sub-project through the normal design flow. Each sub-project gets its own spec → plan → implementation cycle.
- For appropriately-scoped projects, ask questions one at a time to refine the idea
- Prefer multiple choice questions when possible, but open-ended is fine too
- Only one question per message - if a topic needs more exploration, break it into multiple questions
- Focus on understanding: purpose, constraints, success criteria

**Exploring approaches:**
```
- `new_string`:
```
- If the project is too large for a single spec, help the user decompose into sub-projects: what are the independent pieces, how do they relate, what order should they be built? Then brainstorm the first sub-project through the normal design flow. Each sub-project gets its own spec → plan → implementation cycle.
- For appropriately-scoped projects, ask questions one at a time to refine the idea
- Prefer multiple choice questions when possible, but open-ended is fine too
- Only one question per message - if a topic needs more exploration, break it into multiple questions
- Focus on understanding: purpose, constraints, success criteria

## Reading Exploration Context

Before asking clarifying questions, read the project's exploration sediment:

- Read `docs/superpowers/explorations/INDEX.md`. If it has entries that look relevant to the current brainstorm topic, Read those exploration files.
- Optionally read `DOCUMENTS.md` to know which existing project docs are off-limits or high-signal.
- **Empty, missing, or no relevant entries: proceed normally.** Do not prompt the user to run `/explore` from here.
- **If a relevant exploration exists: cite it explicitly** in later steps, e.g. "I see INDEX.md has an auth-flow exploration — does this need to integrate with that?" or "Your existing data-model exploration covers user tables — should this new feature extend it or stand alone?"
- **🟠 documents in DOCUMENTS.md: never read.** Follow the explore skill's "Read a 🟠 document without first asking the user" red flag.
- **🟢/🟡 documents whose description is clearly off-topic: don't read.** Follow the explore skill's same rule.
- **Never invent citations.** Only cite what was actually in the read files.
- **The read is one-shot per session.** When the topic shifts within a brainstorm, re-read only if the new topic doesn't overlap with the previous one's relevant explorations; otherwise reuse what was read. Agent's call.
- **If the user contradicts a cited exploration** (e.g., "X 探索说走 JWT,其实我们走 cookie"), the user's latest statement wins. If a design is committed, optionally add a `## 代码 vs 文档` section to record the conflict (mirrors the explore skill's pattern).

**Exploring approaches:**
```
- `replace_all`: false

- [ ] **Step 3: Verify the edit applied correctly**

Run:
```bash
sed -n '76,100p' skills/brainstorming/SKILL.md
```

Expected: the new section `## Reading Exploration Context` appears between the last bullet of "Understanding the idea" (`- Focus on understanding: purpose, constraints, success criteria`) and the `**Exploring approaches:**` sub-heading. The section header uses `##` (h2), and the section contains exactly 9 bullet points (the 9 spec items).

- [ ] **Step 4: Confirm the diff is exactly the expected change**

Run:
```bash
git diff skills/brainstorming/SKILL.md
```

Expected: 11 lines added (1 blank + 1 h2 header + 9 bullets), 0 removed. The diff should NOT show this commit combined with Task 1's commit (Task 1 was already committed). If Task 1's diff is also present, that means Task 1 wasn't committed — stop and resolve before continuing.

- [ ] **Step 5: Commit**

```bash
git add skills/brainstorming/SKILL.md
git commit -m "$(cat <<'EOF'
docs(brainstorming): add 'Reading Exploration Context' section

Documents the 1a sub-step's full behavior: which files to read, when
to skip silently, how to cite relevant findings, and the discipline
rules (never read 🟠, never invent citations, one-shot per session,
user's latest statement wins on conflict).

Mirrors the explore skill's red flags for 🟠/🟡/🟢/🔴 document
handling so the integration stays consistent with the sediment
convention established in the explore skill.

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
EOF
)"
```

---

## Task 3: Update `## Process Flow` dot graph

**Files:**
- Modify: `skills/brainstorming/SKILL.md` (the dot graph inside `## Process Flow`, currently around lines 36–64)

- [ ] **Step 1: Read the current dot graph to find the exact insertion point**

Run:
```bash
cd "$(git rev-parse --show-toplevel)" && sed -n '36,65p' skills/brainstorming/SKILL.md
```

Expected: a `\`\`\`dot` code block. The graph declares nodes (lines like `"Explore project context" [shape=box];`) and edges (lines like `"X" -> "Y";`). We need to:

1. Add a node declaration for "Read exploration context"
2. Add an edge `"Explore project context" -> "Read exploration context";`
3. Add an edge `"Read exploration context" -> "Visual questions ahead?";`

The first existing edge in the current graph is:
`"Explore project context" -> "Visual questions ahead?";`

After our change, this edge should be **replaced** with the new edges (the path from "Explore project context" should now go through "Read exploration context" before reaching "Visual questions ahead?").

- [ ] **Step 2: Add the new node declaration**

Use the Edit tool with:
- `file_path`: `skills/brainstorming/SKILL.md`
- `old_string`:
```
    "Explore project context" [shape=box];
    "Visual questions ahead?" [shape=diamond];
```
- `new_string`:
```
    "Explore project context" [shape=box];
    "Read exploration context" [shape=box];
    "Visual questions ahead?" [shape=diamond];
```
- `replace_all`: false

- [ ] **Step 3: Add the two new edges (replacing the direct edge to "Visual questions ahead?")**

Use the Edit tool with:
- `file_path`: `skills/brainstorming/SKILL.md`
- `old_string`:
```
    "Explore project context" -> "Visual questions ahead?";
    "Visual questions ahead?" -> "Offer Visual Companion\n(own message, no other content)" [label="yes"];
```
- `new_string`:
```
    "Explore project context" -> "Read exploration context";
    "Read exploration context" -> "Visual questions ahead?";
    "Visual questions ahead?" -> "Offer Visual Companion\n(own message, no other content)" [label="yes"];
```
- `replace_all`: false

- [ ] **Step 4: Verify the dot graph renders the right structure**

Run:
```bash
sed -n '36,70p' skills/brainstorming/SKILL.md
```

Expected: 3 new lines in the dot graph block:
- `    "Read exploration context" [shape=box];` (between existing node declarations)
- `    "Explore project context" -> "Read exploration context";` (new edge)
- `    "Read exploration context" -> "Visual questions ahead?";` (new edge)

The original edge `"Explore project context" -> "Visual questions ahead?";` should be **gone** (replaced by the chain through "Read exploration context").

- [ ] **Step 5: Confirm the diff is exactly the expected change**

Run:
```bash
git diff skills/brainstorming/SKILL.md
```

Expected: exactly **3 lines added** (1 new node + 2 new edges) and **1 line removed** (the original direct edge). All changes inside the `\`\`\`dot` code block. No other files in the diff.

If the diff shows more than 4 lines changed, revert (`git checkout -- skills/brainstorming/SKILL.md`) and re-apply Steps 2 and 3.

- [ ] **Step 6: Optional dot-render sanity check**

If `graphviz` is installed (`dot -V` works), render the graph to verify it's still valid:

```bash
sed -n '/^```dot/,/^```$/p' skills/brainstorming/SKILL.md | sed '1d;$d' | dot -Tsvg -o /tmp/brainstorm-flow.svg
```

Expected: `/tmp/brainstorm-flow.svg` is created without errors. Open it briefly to confirm the "Read exploration context" node appears between "Explore project context" and "Visual questions ahead?".

If `dot` is not installed, skip this step. The 3-line diff is small enough to verify by inspection.

- [ ] **Step 7: Commit**

```bash
git add skills/brainstorming/SKILL.md
git commit -m "$(cat <<'EOF'
docs(brainstorming): add 'Read exploration context' to Process Flow graph

The dot graph now shows "Read exploration context" as a sub-step of
"Explore project context" (which it is — it's sub-item 1a in the
checklist). The flow is:

  Explore project context
    → Read exploration context
    → Visual questions ahead?

The original direct edge is replaced (not added alongside) so the
graph still shows a single linear path through Step 1.

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
EOF
)"
```

---

## Task 4: Verify against the spec (12-scenario walkthrough)

**Files:**
- Read: `skills/brainstorming/SKILL.md` (the modified file)
- Read: `docs/superpowers/specs/2026-06-13-brainstorming-consults-explore-design.md` (the spec, for cross-reference)

- [ ] **Step 1: Read the final state of the modified file**

Run:
```bash
cd "$(git rev-parse --show-toplevel)" && wc -l skills/brainstorming/SKILL.md && echo "---" && git log --oneline -4 -- skills/brainstorming/SKILL.md
```

Expected:
- File is ~180 lines (was 165 before the 3 commits; ~15 lines added).
- Last 3 commits on this file are the ones from Tasks 1, 2, 3 in chronological order.

- [ ] **Step 2: Confirm overall diff totals**

Run:
```bash
git diff 3f80f1c..HEAD -- skills/brainstorming/SKILL.md | grep -E '^[+-]' | grep -v '^[+-]{3}' | wc -l
git diff --stat 3f80f1c..HEAD -- skills/brainstorming/SKILL.md
```

(`3f80f1c` is the commit on `main` where brainstorming/SKILL.md was last touched; adjust if you've rebased.)

Expected: ~15 lines added, 0 lines removed, 1 file changed.

- [ ] **Step 3: Walk through the 12 spec verification scenarios**

For each scenario below, read the relevant section of the modified `skills/brainstorming/SKILL.md` and confirm the spec's behavior is implemented. The table mirrors the spec's "Spec walkthrough" table — a 12/12 PASS is the acceptance gate.

| # | 场景 | What to check in `SKILL.md` | Pass criteria |
|---|---|---|---|
| 1 | `INDEX.md` 缺失 | Edit 2: "Empty, missing, or no relevant entries: proceed normally" | PASS if "missing" appears in the section |
| 2 | `INDEX.md` 0 项 | Same as #1 | PASS |
| 3 | `INDEX.md` 有 N 项,0 项相关 | "proceed normally. Do not prompt the user to run `/explore` from here" | PASS if no-prompt language exists |
| 4 | `INDEX.md` 有 N 项,部分沾边 | "Default only read obviously relevant; agent's call" | PASS if agent's-call language exists |
| 5 | `INDEX.md` 链接坏 | Read errors naturally degrade to "no content for this entry" (no explicit mention needed — Read tool failure is implicit) | PASS by absence of bad behavior, not by spec text |
| 6 | `DOCUMENTS.md` 含 🟠 | "🟠 documents in DOCUMENTS.md: never read" | PASS if 🟠 rule is present |
| 7 | `DOCUMENTS.md` 含 🟢/🟡 off-topic | "🟢/🟡 documents whose description is clearly off-topic: don't read" | PASS if 🟢/🟡 rule is present |
| 8 | 单 exploration 文件 > 200 行 | Implicit in "Read those exploration files" + agent's call | PASS by agent judgment being the model |
| 9 | 切子主题 | "The read is one-shot per session. When the topic shifts ... Agent's call" | PASS if one-shot language exists |
| 10 | mid-brainsorm `/explore` | "Do not prompt the user to run `/explore` from here" (反向:尊重显式触发) | PASS by absence of interference |
| 11 | 用户反驳 cited exploration | "the user's latest statement wins" | PASS if user-wins language exists |
| 12 | 主题跟项目无关 | "no relevant entries: proceed normally" | PASS by same rule as #3 |

Mark each row PASS or FAIL inline. If any row is FAIL, fix the spec text in the new section and re-run this step.

- [ ] **Step 4: Confirm no unintended files were changed**

Run:
```bash
git diff --stat 3f80f1c..HEAD
```

Expected: only `skills/brainstorming/SKILL.md` in the diff. The 3 new commits should NOT have touched:
- `skills/explore/SKILL.md`
- `docs/superpowers/explorations/` (those are explored outputs, not source)
- Any harness plugin config
- `package.json` or other root files

- [ ] **Step 5: Commit verification (no code change)**

If Task 4 is a clean pass, no further commit is needed. Mark Task 4 complete in the todo list.

(Optional) Tag the merge point:

```bash
git tag brainstorming-consults-explore-impl
```

This makes it easy to find this exact state for the real-agent smoke test described in the spec.

---

## Self-Review Notes (for the planner, not the implementer)

**Spec coverage check** (run by the planner before handoff):

- ✅ Checklist sub-item 1a → Task 1
- ✅ Reading Exploration Context section → Task 2
- ✅ Dot graph update → Task 3
- ✅ Empty / missing / no relevant → Task 2 step (1)
- ✅ Active citation examples → Task 2 step (2)
- ✅ 🟠/🟢/🟡 rules → Task 2 step (2)
- ✅ One-shot per session → Task 2 step (2)
- ✅ User contradiction handling → Task 2 step (2)
- ✅ Verification scenarios → Task 4

**Placeholder scan** (this plan): No "TBD", "TODO", or vague steps. Every edit has the exact `old_string` and `new_string`. Every command has the expected output.

**Type/name consistency**: All references to "Read exploration context" use the same string (with quotes in the dot graph, without quotes in the bullet text). All file paths use the same `docs/superpowers/explorations/INDEX.md` and `docs/superpowers/explorations/DOCUMENTS.md` form.

**Things the implementer should watch for**:
- The Write tool may not add a trailing newline (well-known harness behavior). Read tool edits do not have this issue, so it shouldn't apply here — but if you copy-paste the new section and re-write the file, run `tail -c 1` to verify it ends with `\n`.
- The dot graph is inside a `\`\`\`dot` code block. Edits must stay inside the block; do not touch surrounding markdown.

---

## Handoff

**Plan complete and saved to `docs/superpowers/plans/2026-06-13-brainstorming-consults-explore.md`.**

Two execution options:

**1. Subagent-Driven (recommended)** — I dispatch a fresh subagent per task, review between tasks, fast iteration with 2-stage review (spec compliance + code quality).

**2. Inline Execution** — Execute tasks in this session using executing-plans, batch execution with checkpoints for review.

Which approach?
