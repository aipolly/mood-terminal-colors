# AGENTS.md

Skill 仓库：Skill 本体在 `skills/`（`SKILL.md` + `reference/*.md`），根目录只放文档。就地修改，不要安装或链接到 `~/.agents/skills/` 等全局目录。

## 约定

- 不写任何脚本（Python/Shell 等可执行代码），Skill 完全依靠 LLM 阅读指令完成；一次性检查用临时命令，不提交。
- `skills/` 下全部内容用英文。`README.md` 为英文默认，顶部链接 `README.zh-CN.md`，中文版链接回英文。
- `reference/` 仅一层 `.md` 文件；技能目录内不放 README 或索引文件。

## 改动流程

改完先 check（frontmatter 完整、单文件 <500 行、`skills/` 无杂项、SKILL.md 引用的章节存在），再提交并推送 `origin/main`。
