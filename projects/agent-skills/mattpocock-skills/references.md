# mattpocock/skills — 官方参考资料

> 核验日期：2026-11-13（UTC+8）
> 本文件优先记录上游一手资料。第三方介绍仅作为发现入口，不作为关键事实的唯一依据。

## 1. 项目入口

- GitHub 仓库：https://github.com/mattpocock/skills
- README：https://github.com/mattpocock/skills/blob/main/README.md
- Releases：https://github.com/mattpocock/skills/releases
- 最新 Release（核验时 v1.3.2）：https://github.com/mattpocock/skills/releases/tag/v1.3.2
- Changelog：https://github.com/mattpocock/skills/blob/main/CHANGELOG.md
- License：https://github.com/mattpocock/skills/blob/main/LICENSE
- 项目主页：https://aihero.dev/skills

## 2. 技能目录与分发配置

- Engineering Skills：https://github.com/mattpocock/skills/tree/main/skills/engineering
- Productivity Skills：https://github.com/mattpocock/skills/tree/main/skills/productivity
- Misc Skills：https://github.com/mattpocock/skills/tree/main/skills/misc
- In Progress / Beta：https://github.com/mattpocock/skills/tree/main/skills/in-progress
- Claude Plugin Manifest：https://github.com/mattpocock/skills/blob/main/.claude-plugin/plugin.json
- Claude Marketplace Manifest：https://github.com/mattpocock/skills/blob/main/.claude-plugin/marketplace.json
- Agent Skill 写作参考（writing-for-agents）：https://github.com/mattpocock/skills/tree/main/skills/productivity/writing-for-agents

## 3. 重点 Skill 原文

- ask-matt（主流程路由）：https://github.com/mattpocock/skills/blob/main/skills/engineering/ask-matt/SKILL.md
- grill-with-docs：https://github.com/mattpocock/skills/blob/main/skills/engineering/grill-with-docs/SKILL.md
- setup-matt-pocock-skills：https://github.com/mattpocock/skills/blob/main/skills/engineering/setup-matt-pocock-skills/SKILL.md
- to-spec：https://github.com/mattpocock/skills/blob/main/skills/engineering/to-spec/SKILL.md
- to-tickets：https://github.com/mattpocock/skills/blob/main/skills/engineering/to-tickets/SKILL.md
- implement：https://github.com/mattpocock/skills/blob/main/skills/engineering/implement/SKILL.md
- implement-spec：https://github.com/mattpocock/skills/blob/main/skills/engineering/implement-spec/SKILL.md
- tdd：https://github.com/mattpocock/skills/blob/main/skills/engineering/tdd/SKILL.md
- diagnosing-bugs：https://github.com/mattpocock/skills/blob/main/skills/engineering/diagnosing-bugs/SKILL.md
- code-review：https://github.com/mattpocock/skills/blob/main/skills/engineering/code-review/SKILL.md
- wayfinder：https://github.com/mattpocock/skills/blob/main/skills/engineering/wayfinder/SKILL.md
- handoff：https://github.com/mattpocock/skills/blob/main/skills/productivity/handoff/SKILL.md
- writing-for-agents：https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-for-agents/SKILL.md

## 4. 安装入口（以官方 README 当前内容为准）

- Claude Code Plugin：https://docs.anthropic.com/en/docs/claude-code/plugins
- Codex Plugins：https://developers.openai.com/codex/plugins
- Skills CLI：https://skills.sh/
- Gemini CLI：https://github.com/google-gemini/gemini-cli
- GitHub Copilot CLI：https://docs.github.com/en/copilot/concepts/agents/about-agent-skills

## 5. 版本与维护核验

- 最新 Release API：https://api.github.com/repos/mattpocock/skills/releases/latest
- 仓库元数据 API：https://api.github.com/repos/mattpocock/skills
- 主分支递归文件树 API：https://api.github.com/repos/mattpocock/skills/git/trees/main?recursive=1
- 插件 Manifest 中列出正式插件分发的 Skills；不要将 misc、in-progress 或 deprecated 内容自动视为插件包内容。
- 仓库包含 CHANGELOG、Changesets 配置及插件版本同步脚本；版本判断应同时检查 Release、主分支和 Manifest。

## 6. 证据边界

- 安装命令、技能数量、插件包含范围和更新行为以核验时上游 README / Manifest 为准。
- 不同宿主的插件系统与 Agent Skills 实现并不完全相同；README 中的安装说明不能代替宿主当前版本的官方文档。
- 本档案未声称已在用户设备上完成所有宿主安装与端到端实测。
- 项目 Star / Fork 数和主分支提交会变化，不作为长期固定指标。
