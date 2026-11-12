# agent-browser 参考资料

> 核验日期：2026-11-12。本文档以 v0.42.0（2026-11-10 Release）为研究基线。上游命令、配置和工具 schema 会变化，使用前请复核。

## 上游仓库与版本

- [GitHub 仓库](https://github.com/vercel-labs/agent-browser)
- [Releases](https://github.com/vercel-labs/agent-browser/releases)
- [v0.42.0 Release](https://github.com/vercel-labs/agent-browser/releases/tag/v0.42.0)
- [LICENSE](https://github.com/vercel-labs/agent-browser/blob/main/LICENSE)

## 官方文档

- [官方文档首页](https://agent-browser.dev/)
- [Quick Start](https://agent-browser.dev/quick-start)
- [Installation](https://agent-browser.dev/installation)
- [Commands](https://agent-browser.dev/commands)
- [Configuration](https://agent-browser.dev/configuration)
- [Security](https://agent-browser.dev/security)
- [MCP](https://agent-browser.dev/mcp)
- [Skills](https://agent-browser.dev/skills)
- [Selectors](https://agent-browser.dev/selectors)
- [Snapshots](https://agent-browser.dev/snapshots)
- [WebMCP](https://agent-browser.dev/webmcp)
- [Plugins](https://agent-browser.dev/plugins)

## 本次档案主要核验点

| 主题 | 参考来源 | 核验内容 |
|---|---|---|
| 安装与运行环境 | Installation | npm 全局/本地安装、浏览器下载、doctor 检查项 |
| CLI 能力 | Commands | 导航、读取、快照、交互、截图、批处理、MCP 启动方式 |
| 配置优先级 | Configuration | 用户级/项目级配置、环境变量、CLI 参数优先级 |
| 安全 | Security | 默认 opt-in 状态、威胁模型和防护能力 |
| MCP | Commands / MCP | stdio 传输、工具 profile 和工具范围 |
| Skill | Skills | discovery skill、CLI 动态获取版本匹配说明 |
| 版本 | Releases | v0.42.0 发布说明与变更 |

## 证据边界

- 官方文档用于确认上游公开支持的功能、命令和配置。
- README 中的架构说明、选型比较和工作流建议属于工程分析。
- 本档案未将未经目标 Windows 环境执行验证的命令标记为“实测通过”。
- Provider、不同 MCP 宿主和安全策略的实际行为应在目标环境分别验证。

## 维护提醒

出现新 Release、CLI 命令变化、MCP schema 变化、Windows 安装变化、安全公告或许可证变化时，应复核 README 和本文件，并在提交记录中说明变更。
