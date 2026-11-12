# agent-browser

> 面向 AI Agent 的浏览器自动化 CLI：通过命令行驱动浏览器，读取页面可访问性结构并执行交互。

- **收录状态**：recommended
- **项目标签**：浏览器自动化、CLI、MCP、Skill、AI 项目
- **上游仓库**：https://github.com/vercel-labs/agent-browser
- **官方文档**：https://agent-browser.dev/
- **许可证**：Apache-2.0（以当前上游仓库为准）
- **当前核验版本**：v0.42.0 Release（2026-11-10；写档时核验）
- **核验日期**：2026-11-12
- **文档状态**：研究档案初版；命令示例需结合实际安装版本复核

## 1. 项目定位与价值

agent-browser 是 Vercel Labs 开源的浏览器自动化工具。它不是一个完整的 AI Agent，而是为 Agent 或脚本提供可编程的浏览器控制能力。它可以打开网页、读取页面内容、定位元素、点击、填写表单、截图、下载文件，并提供会话、认证和安全策略等能力。

核心价值在于：把浏览器操作转换成 Agent 易于理解和调用的命令接口，并通过紧凑的页面快照降低上下文开销。

**推荐收录理由**：功能覆盖面广，支持 Windows/macOS/Linux，提供 CLI、MCP 和 Skills 等接入方式；适合作为浏览器自动化基础设施，也有较高的工程参考价值。

## 2. 核心功能

- 页面导航、前进/后退、刷新。
- 读取页面文本与可访问性树快照。
- 通过元素引用、语义定位器、CSS 等方式查找和操作元素。
- 点击、输入、选择、勾选、滚动、拖拽、上传。
- 等待页面状态、元素或 URL 变化。
- 截图、PDF、录制、下载和页面差异比较。
- Cookie、Storage、网络请求、代理和页面状态管理。
- 多标签页、iframe、对话框和会话隔离。
- 调试与性能能力，例如 console、errors、trace、profiler、vitals、a11y 等。
- Auth Vault、域名白名单、动作策略、敏感动作确认、内容边界和输出长度限制。
- MCP Server、Skills、WebMCP、flow、batch、dashboard 等 Agent 集成入口。

具体命令以当前版本的 `agent-browser --help` 和官方命令参考为准。

## 3. 工作原理

整体采用 CLI + 后台 daemon + 浏览器的结构：

1. Agent 或用户执行 `agent-browser` 命令。
2. Rust CLI 解析命令，并与后台 daemon 通信。
3. daemon 通过 Chrome DevTools Protocol（CDP）控制浏览器。
4. 浏览器执行导航、读取或交互操作。
5. 结果以文本/JSON 等形式返回给 CLI，再交给 Agent。

### Snapshot 与 refs

`snapshot -i` 会返回可交互元素的紧凑描述，并为元素分配类似 `@e1` 的引用。Agent 可以使用引用操作元素，而不必每次构造复杂选择器。

**重要：** refs 只对应生成快照时的页面状态。导航或 DOM 发生变化后，旧引用可能失效；执行关键操作前应重新 snapshot 并验证结果。

### 浏览器与运行模式

默认使用 Chrome for Testing；也可以连接已有 Chrome（CDP）、选择支持的浏览器引擎或使用受支持的云浏览器提供商。具体 provider、平台和限制应查看官方文档。

## 4. 安装指南（Windows）

### 环境要求

- Windows x64 有对应原生二进制。
- 推荐 Node.js/npm 用于通过 npm 安装 CLI。
- 浏览器首次运行前需执行安装命令下载 Chrome for Testing；也可使用已检测到的兼容浏览器。
- 从源码构建才需要 Node.js 24+、pnpm 11+ 和 Rust。普通 npm 安装不要求用户自行构建 Rust 程序。

### 全局安装（推荐入门方式）

在 PowerShell 执行：

```powershell
npm install -g agent-browser
agent-browser install
agent-browser --help
agent-browser doctor
```

说明：
- 第一条安装全局 CLI。
- 第二条下载并准备 Chrome for Testing（首次安装需要）。
- `doctor` 用于检查 CLI、浏览器、配置和运行环境。

### 临时试用

无需全局安装：

```powershell
npx agent-browser open https://example.com
```

如果计划长期使用，建议固定版本并采用全局安装或项目本地依赖，避免每次运行都临时解析版本。

### 更新

```powershell
agent-browser upgrade
```

升级前可先记录当前版本和配置。升级行为会根据安装方式选择对应的包管理器。

### 安装位置与磁盘占用

npm 全局包的位置由 npm prefix 决定；浏览器缓存、配置、会话和认证数据通常位于用户目录下的 agent-browser 数据目录。若希望避免占用系统盘，应先检查 npm prefix 和工具支持的配置/环境变量，再调整；不要未经确认就手动移动运行中的缓存或凭证目录。

可检查：

```powershell
npm config get prefix
npm root -g
agent-browser doctor
```

## 5. 快速使用教程

```powershell
# 打开网页
agent-browser open https://example.com

# 获取交互元素快照
agent-browser snapshot -i

# 对快照中的元素执行操作（@e1 仅为示例，按实际输出替换）
agent-browser click @e1

# 获取当前 URL
agent-browser get url

# 关闭浏览器会话
agent-browser close
```

### 推荐工作循环

1. 打开目标页面。
2. 执行 snapshot，理解页面结构。
3. 选择元素并执行一个操作。
4. 再次 snapshot 或读取页面状态，确认操作结果。
5. 继续下一步；出现导航或页面结构变化时重新获取 refs。
6. 完成后关闭会话。

不要假设点击命令返回就意味着业务操作已经成功；应读取页面状态、提示信息或目标数据进行验证。

## 6. Skills 与 MCP 集成

### Skills

项目自带与版本匹配的 Skill 内容：

```powershell
# 获取核心使用说明
agent-browser skills get core

# 获取命令参考
agent-browser skills get core --ref commands

# 获取完整核心资料
agent-browser skills get core --full

# 查看可用的专项 Skills
agent-browser skills list
```

也可通过 skills.sh 安装 Agent 侧的发现入口：

```powershell
npx skills add vercel-labs/agent-browser
```

Skill 主要提供 Agent 如何正确使用工具的指引；它本身不等于浏览器执行引擎。

### MCP

启动 stdio MCP Server：

```powershell
agent-browser mcp
```

MCP 客户端通常需要在配置中声明可执行命令和参数。通用配置形态如下（具体配置文件位置与字段名称取决于宿主客户端）：

```json
{
  "mcpServers": {
    "agent-browser": {
      "command": "agent-browser",
      "args": ["mcp"]
    }
  }
}
```

如果宿主环境找不到全局命令，可根据客户端支持情况使用可执行文件绝对路径，或配置为通过 `npx` 启动。MCP 工具清单、profile 和工具过滤参数以当前版本官方 MCP 文档为准。

**概念区分：**
- CLI：实际执行浏览器操作的命令入口。
- MCP：把工具能力以协议形式提供给支持 MCP 的 Agent 宿主。
- Skill：告诉 Agent 如何发现、理解和正确调用这些能力。

本地 stdio MCP 不是远程 MCP 服务，不能据此推断 ChatGPT 网页版可以直接连接它。必须由宿主支持对应 MCP 接入方式，并能启动该进程或连接到受支持的远程服务。

## 7. 适用场景

- 网页内容读取、信息提取和跨页面操作。
- 网页表单填写、重复性后台操作。
- Web 应用端到端测试与回归验证。
- Agent 需要使用真实浏览器而非仅调用 HTTP API 的任务。
- 对自建 Web 项目执行交互验收、截图和问题复现。

对 GitHub 仓库、Issue、PR 等结构化操作，优先使用 GitHub API 或官方连接器；只有必须模拟网页用户操作时才考虑浏览器自动化。

## 8. 限制与风险

- 页面结构和异步加载会影响定位；需要合理等待并验证状态。
- refs 可能因导航或 DOM 替换失效。
- CAPTCHA、MFA、反机器人策略可能要求人工介入，不能保证自动通过。
- 浏览器自动化可能访问已登录账户、读取页面敏感信息或执行高影响操作。
- 网页内容可能包含 Prompt Injection；页面文本必须视为不可信输入。
- 多个并行浏览器会话会增加 CPU、内存和浏览器资源占用。
- 域名白名单、动作策略等安全能力需要主动配置；官方说明指出安全功能默认并非全部启用。
- 域名白名单不是操作系统级网络防火墙；部分 provider/浏览器模式存在限制。生产环境应结合容器、网络出口策略和最小权限。

### 安全建议

- 对自动化目标设置域名白名单。
- 使用最小化动作策略，限制 eval、上传、下载等高风险操作。
- 对敏感动作启用确认机制。
- 启用内容边界和输出长度限制。
- 不要把密码、Vault Token 或 API Key 写进命令行参数、仓库文件或普通配置。
- 使用专用低权限账户和独立浏览器会话；避免直接复用包含个人敏感数据的日常浏览器配置。

## 9. 维护情况与许可证

上游在 2026-11-10 发布 v0.42.0。该版本 Release 记录了多项浏览器会话、Safari/iOS、MCP、文本输入和 Windows 录制相关修复，并调整了包管理与运行时要求。项目仍在活跃迭代，命令和配置可能变化。

许可证：Apache-2.0。再分发、修改或集成时仍应保留许可证和版权声明，并核对上游最新条款。

## 10. 综合评估

**结论：推荐收录，值得实际试用。**

优势：
- 面向 Agent 设计，命令行接口清晰。
- Rust CLI/daemon 与 CDP 控制模式适合自动化工作流。
- 同时提供 CLI、MCP、Skills，多种 Agent 宿主可选择。
- 具有会话、认证和安全策略能力，不只是简单点击脚本。

不足与成本：
- 需要理解浏览器状态、异步页面和定位策略。
- 高风险自动化必须额外设计权限、安全和人工确认。
- 不同云 provider 和移动端模式存在功能差异。
- 版本更新较快，文档和宿主配置需要跟随版本核验。

## 11. 封装与集成评估

**值得列入集成候选，但当前不建议直接开发 ChatGPT 专用封装。**

最合适的近期形态：
1. 在支持本地命令/MCP 的 Agent 客户端中直接使用上游 CLI/MCP。
2. 配套安装官方 Skill，提升 Agent 的调用可靠性。
3. 若未来需要多人或云端统一使用，再开发受控的远程服务/适配层。

不建议一开始 Fork 上游。优先保留上游更新路径，在外层增加任务编排、访问策略、审计日志、会话隔离和凭证管理。

### ChatGPT 可行性边界

项目提供 stdio MCP，并不等于当前 ChatGPT 账号一定存在可连接本地 stdio MCP 的入口。若宿主只支持远程 MCP，则还需要把服务部署到可访问的环境，并实现认证、授权、网络隔离和安全审计。部署远程服务会显著扩大浏览器访问能力的风险面。

### 与当前在线工具平台的关系

可用于开发阶段的自动化验收，例如打开 GitHub Pages 部署页面、检查关键页面、执行交互、截图和验证导航。它不应成为 GitHub 内容写入或仓库管理的首选通道；结构化 GitHub 操作优先走 GitHub API/连接器。

## 12. 后续实践建议

- 在 Windows 环境安装并运行 `doctor`。
- 用公开、无登录要求的网站练习 snapshot/ref/交互循环。
- 在 Trae 或其他支持 MCP 的客户端配置 stdio 服务。
- 先测试只读任务，再逐步测试表单和下载等有副作用的操作。
- 对安全策略进行独立验证，不要把默认配置视为安全配置。

## 13. 官方资料

见同目录 [references.md](./references.md)。
