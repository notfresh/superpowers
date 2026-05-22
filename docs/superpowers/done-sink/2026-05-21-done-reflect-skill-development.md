# done-sync 技能开发 (2026-05-21)

## 发现 (What we discovered)
- Superpowers 项目结构：skills/ 目录存放技能实现，docs/superpowers/ 存放文档
- 技能文件格式：YAML frontmatter + markdown 正文
- 已有的工作流技能：finishing-a-development-branch, requesting-code-review, executing-plans, subagent-driven-development
- 四个技能都已集成 done-sync 触发点

## 学到 (What we learned)
- 技能集成方式：在目标技能的流程中添加触发点，调用 `Use superpowers:done-sync`
- done-sync 目录：`docs/superpowers/done-sync/`，文件名格式 `YYYY-MM-DD-<任务名>.md`
- 触发时机：任务完成后、review 完成后、plan 执行完成后都可以触发

## 完成 (What we accomplished)
- 创建了 `skills/done-sync/SKILL.md` 技能文件
- 集成到 4 个工作流技能：finishing-a-development-branch, requesting-code-review, executing-plans, subagent-driven-development
- 创建了示例文件 `docs/superpowers/done-sync/2026-05-21-example-reflection.md`
- 提交了 6 个 commit，完成了完整的 design → plan → implementation 流程