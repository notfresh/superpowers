# 添加 explore 技能 (2026-06-13)

## 发现 (What we discovered)
- 项目历史里 v3-5.0 系统性退役了 slash commands,但退役的是**重复 skill 的命令**(`brainstorm.md`、`write-plan.md` 等),不是禁用所有 slash command——这是评审员最初误读的地方
- `using-superpowers` 和 `test-driven-development` 的反合理化表里把 "explore" 列为借口,但仔细读上下文:它们禁止的是"**无 skill 的探索**",`explore` skill 本身正是纪律要求的那个 skill——C3 撤回的根因
- 项目的"沉淀物"模式:`docs/superpowers/done-sink/`、`docs/superpowers/specs/`、`docs/superpowers/plans/`、`docs/superpowers/explorations/` 都按子目录组织
- 项目天然支持隔离:`.worktrees/` 已在 `.gitignore`,`git worktree add` 直接建隔离工作区
- 多 harness 插件结构:`.claude-plugin/`、`.codex-plugin/`、`.cursor-plugin/`、`.opencode/` 各自独立配置

## 学到 (What we learned)
- **评审员也会错,根理解需要核实**:最终全量审查抛的 3 个 Critical,1 个是真问题(C2 领域特定 vs 通用),1 个是设计选择被误读为违规(C1 slash command),1 个是反合理化表被误读为反探索(C3)——**逐个 git log / grep 验证**后才看清
- **Write 工具有个坑**:内容不以 `\n` 结尾,文件就不带末尾换行,需要 `printf '\n' >>` 补——Task 1 的 trailing newline bug 就是这么抓到的
- **纯文档变更的"测试"是 spec 走查**:8 个验证场景的 PASS = 读 SKILL.md 看文案能否走通,不是真跑 agent。这是文档技能能用的最严验证
- **2 阶段审查真的能抓到问题**:Task 1 那个 trailing newline bug 是规范审查没抓到、代码质量审查抓到的;如果跳过 code review,bug 就进 PR 了
- **CLAUDE.md 的 94% PR 拒收率不是吓唬人**:评审员引用的项目历史是真实的——v3-5.0 真的一路在退 slash command、CLAUDE.md 真有"领域特定不属于 core"的明文

## 完成 (What we accomplished)
- 在 worktree `feature/explore-skill` 上落了 5 个 commit:
  - `d8bf9f4` feat(explore): 2 个种子文件(INDEX.md / DOCUMENTS.md)
  - `74a6731` fix(explore): 给种子文件加 trailing newlines
  - `7681ab0` feat(explore): Claude Code `/explore` 斜杠命令
  - `781427b` feat(explore): skills/explore/SKILL.md 主体(214 行,8 sections)
  - `4cd944e` docs: 把 8 场景验证结果追加到 spec
- 共 4 个新文件 + 1 处 spec 追加,**0 处现有文件改动**(除 spec 自己)
- 完整 spec + plan + verification report 都已 commit,可作为 PR 的 evidence 包
- C1/C2/C3 三个 Critical 发现:用户拍板保留实现(C1 是设计选择,C2 项目方向一致,C3 根理解被误读)

