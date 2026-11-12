# agent-browser

> 面向 AI Agent、自动化脚本和开发测试流程的浏览器自动化工具。它把真实浏览器的导航、页面理解、交互、状态管理和调试能力封装为 CLI，并可通过 MCP 暴露给支持 MCP 的 Agent 宿主。

- **收录状态**：recommended（推荐收录）
- **项目类型**：浏览器自动化 / 开发者工具 / CLI / Agent 工具
- **AI 项目标记**：是（面向 AI Agent 的工具，不是 AI 模型）
- **集成形态**：CLI、MCP Server、Skill、插件扩展、云浏览器 Provider
- **上游仓库**：[vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser)
- **官方文档**：[agent-browser.dev](https://agent-browser.dev/)
- **许可证**：Apache-2.0（以当前上游仓库 LICENSE 为准）
- **本档案核验版本**：v0.42.0 Release（2026-11-10）
- **本档案核验日期**：2026-11-12
- **文档状态**：研究档案；命令和配置以对应版本官方文档为准
- **实测状态**：本文没有声称已在维护者的 Windows 机器上完成全部命令实测；官方文档确认项、工程分析和建议分别标明

---

## 0. 先给结论

**结论：推荐收录，值得实际试用；作为浏览器自动化底座的价值高于把它当成一个“自动点网页”的小工具。**

agent-browser 解决的是 Agent 如何稳定地使用浏览器：打开页面、理解可访问性结构、定位元素、执行动作、等待状态变化、验证结果，并在需要时管理会话、认证、网络和调试信息。

它的主要优势：

1. CLI 接口直接，既适合人手动试用，也适合脚本和 Agent 调用。
2. 通过 accessibility snapshot 和元素 refs，为 Agent 提供比整页 HTML 更紧凑的页面上下文。
3. 不止提供点击和输入，还覆盖等待、标签页、iframe、网络、Cookie/Storage、截图、PDF、录制、诊断等工作流。
4. 同时提供 CLI、MCP 和 Skill，能适配不同 Agent 宿主。
5. 有会话隔离、认证管理和多种安全策略能力；但安全策略需要主动启用，不能把“有安全功能”误解为“默认安全”。

主要成本：

- 必须理解浏览器状态、异步加载、元素定位和页面变化。
- 自动化动作可能触及登录态、个人数据和真实业务操作，需要权限控制。
- 上游迭代较快，CLI、MCP 工具集和配置项应按版本复核。
- 本地 stdio MCP 只能由支持启动本地 MCP 子进程的宿主使用，不能直接推导出 ChatGPT 网页端可连接。

## 1. 项目定位：它是什么，不是什么

agent-browser 是 Vercel Labs 开源的浏览器自动化工具。它不是一个独立的通用 AI Agent，也不是浏览器本身，更不是只针对某个网站的爬虫。

可以把它理解为一层“浏览器控制接口”：

- 浏览器负责真实页面渲染、JavaScript 执行、Cookie、DOM 和用户交互。
- agent-browser 负责把浏览器能力变成结构化命令。
- Agent 或自动化脚本负责决定下一步做什么、何时操作、如何判断任务完成。

### 1.1 与常见方案的区别

| 方案 | 主要定位 | 典型优点 | 与 agent-browser 的关系 |
|---|---|---|---|
| Playwright | 浏览器自动化开发框架 | API 完整、适合编写测试和复杂程序 | agent-browser 提供更偏 CLI/Agent 的操作接口 |
| Puppeteer | Chrome/Chromium 自动化库 | 面向 Node.js 开发者 | 同属浏览器自动化生态，抽象层不同 |
| Selenium | WebDriver 自动化生态 | 浏览器和语言覆盖面广 | 更传统的自动化测试接口 |
| curl / HTTP 客户端 | 直接请求网络资源 | 轻量、适合 API 和静态资源 | 不等同于真实浏览器，不处理完整交互流程 |
| 浏览器内置/云端 Agent 浏览器 | 由宿主提供浏览器能力 | 宿主集成度高 | agent-browser 可作为另一套可控执行后端 |

**选型原则：** 如果目标网站提供稳定 API，优先使用 API；如果需要真实页面渲染、登录态、前端交互或端到端验收，再考虑浏览器自动化。若项目本身需要复杂测试代码、断言体系和测试报告，直接使用 Playwright 等开发框架可能更合适。

## 2. 功能全景

| 能力域 | 主要用途 | 常见命令/入口 |
|---|---|---|
| 页面导航 | 打开 URL、前进、后退、刷新、关闭 | `open`、`back`、`forward`、`reload`、`close` |
| 页面理解 | 读取渲染文本、可访问性树、交互元素 | `read`、`snapshot` |
| 元素定位 | 按 ref、角色、文本、标签、CSS 等定位 | `click`、`find`、`get`、`is` |
| 页面交互 | 点击、双击、填写、键盘、鼠标、滚动、拖拽 | `click`、`fill`、`type`、`press` |
| 等待与同步 | 等待元素、URL、文本或页面状态 | `wait` 系列命令 |
| 标签页与框架 | 多标签页、窗口、iframe、对话框 | `tab`、`frame`、`dialog` |
| 页面状态 | Cookie、localStorage、sessionStorage、保存/恢复状态 | `cookies`、`storage`、`state` |
| 网络能力 | 请求观察、路由、请求头、HAR、离线模拟 | `network` 相关命令 |
| 输出 | 截图、PDF、录制、下载 | `screenshot`、`pdf`、`record` |
| 调试诊断 | console、页面错误、trace、性能和可访问性检查 | `console`、`errors`、`trace`、`doctor` |
| Agent 集成 | MCP Server、Skill、WebMCP | `mcp`、`skills`、`webmcp` |
| 批处理 | 一次执行多个命令 | `batch` |
| 扩展 | 凭证 Provider、云浏览器 Provider、其他插件能力 | `plugin`、`auth` |

上表是能力分类，不代表每个子命令在所有版本、浏览器引擎和 Provider 上都完全一致。使用前应查看本机 `agent-browser --help` 和官方命令参考。

## 3. 架构与工作原理

### 3.1 逻辑架构

```text
Agent / 用户 / 自动化脚本
          |
          | CLI 命令，或 MCP tool call
          v
  agent-browser CLI / MCP Server
          |
          | 本地进程间通信
          v
   agent-browser daemon
          |
          | CDP（Chrome DevTools Protocol）
          v
   Chrome / Chromium 浏览器
          |
          v
       目标网站
```

核心组件职责：

1. **CLI**：接收命令、解析参数、输出文本或 JSON 结果。
2. **daemon**：管理浏览器进程、会话和浏览器控制连接。后台 daemon 可以让多条 CLI 命令复用浏览器会话，而不是每次都从头启动。
3. **浏览器**：实际加载网页、运行 JavaScript、维护 DOM 和会话状态。
4. **CDP**：Chromium 系浏览器的 DevTools 协议，提供页面、网络、运行时和输入等控制能力。
5. **MCP Server**：把一部分或全部 CLI 能力转换成 MCP 工具，供支持 MCP 的宿主调用。
6. **Skill**：提供工具使用规则、命令知识和工作流提示，不负责真正执行浏览器动作。

### 3.2 一条命令如何执行

例如：

```powershell
agent-browser open https://example.com
```

大致过程：

1. CLI 解析 `open` 和 URL。
2. CLI 连接对应 session 的 daemon；必要时启动 daemon 和浏览器。
3. daemon 通过浏览器控制协议发起导航。
4. 浏览器加载页面。
5. CLI 将执行结果返回给调用者。

后续命令通常可以继续操作同一会话。会话名称、环境变量、配置文件和命令行参数可以影响会话及运行行为，具体以当前版本文档为准。

### 3.3 Snapshot 与 refs：最重要的交互机制

Agent 不应只凭页面截图猜测按钮位置。agent-browser 可以输出页面的 accessibility snapshot，并为可交互元素生成短引用，例如：

```text
- heading "Example Domain" [level=1]
- link "More information" [ref=e1]
```

之后可以：

```powershell
agent-browser click @e1
```

这里的 `@e1` 是当前快照中某个元素的引用，并不是永久 ID，也不是 CSS 选择器。

**必须遵守的工作规则：**

- 每次页面导航后重新获取 snapshot。
- 页面发生明显 DOM 更新、弹窗出现、列表刷新或标签页切换后，重新获取相关页面的 snapshot。
- 不要把旧页面的 `@e1` 当作新页面的同一个元素。
- 操作后读取页面状态或重新 snapshot，确认动作结果。
- 对关键操作使用更明确的角色/文本/标签定位，并在操作前后验证。

这也是 agent-browser 适合 Agent 的关键原因：Agent 可以先读取结构化页面，再决定操作对象，而不是只依赖视觉坐标。

## 4. 安装与运行环境

### 4.1 支持平台与安装路径

官方提供 npm 全局安装、项目本地安装、npx 临时运行、Homebrew 和 Cargo 等方式。Windows 用户通常采用 npm 安装。

官方安装文档说明，全局 npm 安装会安装原生 Rust CLI 二进制；首次使用还需要下载 Chrome for Testing。普通 npm 安装不要求用户自行配置 Rust 编译环境。若从源码构建，则需遵循上游源码构建要求。

### 4.2 Windows 安装（推荐）

在 PowerShell 中执行：

```powershell
# 1. 检查 Node.js 和 npm
node -v
npm -v

# 2. 全局安装
npm install -g agent-browser

# 3. 安装配套浏览器（首次使用）
agent-browser install

# 4. 检查命令是否可用
agent-browser --help
agent-browser --version

# 5. 运行环境诊断
agent-browser doctor
```

说明：

- `npm install -g` 安装 CLI。
- `agent-browser install` 下载/准备 Chrome for Testing。
- `doctor` 检查环境、Chrome、daemon、配置、安全相关文件、Provider 和网络等项目。
- `doctor` 默认用于诊断；不要在不了解影响的情况下随意使用带修复参数的命令。官方文档说明某些修复操作可能重装浏览器或清理旧状态。

### 4.3 不进行全局安装：项目本地依赖

适合希望项目锁定版本、让团队复现相同工具版本的情况：

```powershell
npm install agent-browser
npx agent-browser install
npx agent-browser --help
npx agent-browser open https://example.com
```

优点是版本记录在项目依赖中；缺点是需要在项目目录内通过 `npx` 或 npm scripts 调用。

### 4.4 临时试用

```powershell
npx agent-browser install
npx agent-browser open https://example.com
```

适合快速体验。长期工作流不建议依赖未固定版本的临时解析方式；需要可复现时应固定版本。

### 4.5 Windows 非系统盘安装注意事项

如果希望尽量减少 C 盘占用，先区分三个不同位置：

1. **npm 全局包目录**：由 npm prefix 决定。
2. **浏览器下载/缓存目录**：由 agent-browser/浏览器安装机制决定。
3. **用户配置、daemon 状态、session 数据**：通常位于用户配置目录或工具数据目录。

先检查当前设置：

```powershell
npm config get prefix
npm root -g
$env:USERPROFILE
agent-browser doctor
```

不要把 npm prefix 当成所有数据目录的总开关。修改 npm 全局目录不会自动迁移浏览器缓存、用户配置和认证数据。调整这些目录前，应先查当前版本的 Configuration 文档和实际 `doctor` 输出；不要直接移动正在使用的 session 或凭证文件。

### 4.6 卸载

若通过 npm 全局安装：

```powershell
npm uninstall -g agent-browser
```

这会移除 npm 全局包，但不应假设它会自动删除所有浏览器缓存、用户配置、session 状态或下载文件。清理残留前先确认目录用途，并备份需要保留的数据。

## 5. 从零开始：核心操作教程

下面示例以公开测试网站为目标，不涉及登录、付款或真实业务数据。

### 5.1 打开网页并读取页面

```powershell
agent-browser open https://example.com
agent-browser get url
agent-browser read
agent-browser snapshot
```

理解这些命令的区别：

- `open`：打开浏览器并导航到指定 URL。
- `get url`：读取当前页面 URL。
- `read`：获取面向 Agent 的页面文本内容。
- `snapshot`：获取 accessibility tree 及元素信息，适合进一步定位和交互。

### 5.2 交互元素快照

```powershell
agent-browser snapshot -i
```

`-i` 用于突出交互元素，输出中可能出现按钮、链接、输入框及其 refs。实际 refs 以当前页面输出为准。

### 5.3 点击链接

```powershell
agent-browser snapshot -i
# 假设输出中有：link "More information" [ref=e1]
agent-browser click @e1
agent-browser get url
agent-browser snapshot -i
```

不要直接复制示例里的 `@e1` 到其他网页。每个页面的 ref 都应以刚生成的快照为准。

### 5.4 填写表单

以下是命令形式示例，选择器应按目标网页实际结构调整：

```powershell
agent-browser open https://example.com
agent-browser fill 'input[name="q"]' 'agent-browser'
agent-browser press Enter
agent-browser read
```

常见输入动作：

- `fill`：清空后填入文本，适合表单输入。
- `type`：模拟逐字输入，适合需要触发键入过程的页面。
- `press`：发送键盘按键，例如 Enter、Tab、Control+a。

### 5.5 常用定位策略

优先级建议：

1. snapshot 给出的 ref：适合紧接着快照执行操作。
2. 语义定位：角色、可访问名称、标签或文本，通常更接近用户看到的控件含义。
3. 稳定属性：例如明确的 `data-testid`。
4. CSS selector：适用于开发者明确了解页面 DOM 的情况。
5. 坐标操作：只在语义和 DOM 定位不适用时考虑。

示例写法（具体 API 以命令参考为准）：

```powershell
# ref
agent-browser click @e1

# CSS selector
agent-browser click 'button[type="submit"]'

# 填写指定输入框
agent-browser fill 'input[name="email"]' 'test@example.com'
```

不要为了让脚本“能点到”就选择过于宽泛的 selector，例如页面上第一个 button；这会让页面稍有变化就误操作。

### 5.6 等待异步页面

真实网站常有异步请求、动画、延迟渲染和 SPA 路由变化。不要把固定 sleep 当成首选同步方式。优先等待目标元素、文本、URL 或页面状态满足条件，再继续操作。

命令参考中提供 wait 相关能力；具体子命令、参数和超时写法以当前版本 `agent-browser --help` 为准。

### 5.7 截图和 PDF

```powershell
agent-browser screenshot
agent-browser pdf .\page.pdf
```

截图适合视觉验收和问题记录；PDF 适合保存页面文档。截图输出目录、格式、质量等可以通过 CLI 参数或配置项控制。

### 5.8 结束会话

```powershell
agent-browser close
```

需要关闭所有活动会话时，命令参考提供 `close --all`。自动化脚本应在 finally/清理阶段关闭不再需要的浏览器，避免 daemon 和浏览器长期占用资源。

## 6. 推荐的 Agent 工作循环

浏览器自动化不是“连续发一串 click”就可靠。推荐把每个操作拆成观察、行动和验证：

```text
打开目标页面
    |
读取页面 / snapshot
    |
确定目标元素和操作意图
    |
执行一个动作
    |
等待页面达到预期状态
    |
重新读取页面并验证
    |
还有下一步？ --是--> 回到 snapshot
    |
    否
    v
保存必要结果并关闭会话
```

例如“搜索并打开结果”：

1. 打开搜索页面。
2. snapshot，确认搜索输入框和提交控件。
3. 填写查询内容。
4. 提交。
5. 等待结果列表出现或 URL 改变。
6. 重新 snapshot，确认结果列表结构。
7. 选择目标结果。
8. 导航后重新读取页面，确认标题或 URL。

**不要把工具调用成功等同于业务成功。** 点击返回成功，只说明浏览器执行了点击动作；不代表服务器已经保存、订单已提交或表单已通过验证。业务结果必须通过页面状态、服务端数据或其他可信证据核验。

## 7. 配置体系

### 7.1 配置优先级

官方配置文档说明，配置来源按以下优先级合并/覆盖（高优先级覆盖低优先级）：

1. 用户级配置：`~/.agent-browser/config.json`
2. 项目级配置：`./agent-browser.json`
3. 环境变量：`AGENT_BROWSER_*`
4. CLI 参数：优先级最高

也可以通过 `--config <path>` 或 `AGENT_BROWSER_CONFIG` 指定配置文件。

### 7.2 示例配置

以下是一个面向本地开发和 Agent 使用的示意配置。域名和路径必须替换为自己的实际目标：

```json
{
  "headed": true,
  "contentBoundaries": true,
  "maxOutput": 50000,
  "allowedDomains": [
    "localhost",
    "127.0.0.1",
    "your-app.example.com"
  ]
}
```

字段说明：

- `headed`：显示浏览器窗口，方便观察自动化过程。
- `contentBoundaries`：在页面输出中加入边界标记，帮助 Agent 区分网页内容与指令。
- `maxOutput`：限制输出长度，减少过量页面内容进入上下文。
- `allowedDomains`：限制允许访问的域名范围。实际支持的匹配规则和模式限制应按安全文档核验。

### 7.3 常见配置项类别

- 浏览器启动：headed、executablePath、args、userAgent、proxy、viewport 等。
- 运行控制：defaultTimeout、idleTimeout、session 等。
- 页面输出：json、annotate、contentBoundaries、maxOutput。
- 文件：downloadPath、screenshotDir、screenshotFormat、screenshotQuality。
- 安全：allowedDomains、actionPolicy、confirmActions、confirmInteractive。
- 扩展：plugins、extensions、initScripts、provider。

配置项名称和 JSON 字段以当前版本官方 Configuration 页面为准。不要从其他版本复制一份未经核对的完整配置。

## 8. 会话、状态和认证

### 8.1 Session 隔离

多任务并行时，应使用独立 session，避免不同任务共享标签页、Cookie 或页面状态。MCP 工具调用也可以通过 session 参数或环境变量选择会话。

建议：

- 每个独立任务使用明确的 session 名称。
- 不要让互不信任的任务共享同一个已登录 session。
- 任务结束后关闭会话。
- 不要把 session 状态文件提交到 Git 仓库。

### 8.2 Cookie 与 Storage

工具提供 Cookie、localStorage 等状态读取和操作能力。它们对调试登录态和测试应用状态很有用，但也可能暴露会话凭证。

原则：

- 只在自己有权访问的站点和账户上操作。
- 不要把完整 Cookie、Bearer Token 或 session 内容贴入公开 Issue、日志和仓库。
- 测试环境优先使用专用测试账户。
- 生产账户操作应限制为必要的只读行为，避免在自动化中保存或导出敏感状态。

### 8.3 Auth Vault 与凭证 Provider

项目提供认证相关命令和凭证 Provider 扩展机制。其设计目标是避免把密码直接放进 Agent 上下文：凭证由本地工具/Provider 处理，再用于登录流程。

注意：

- 凭证 Provider 插件在独立进程中运行，但这不代表插件天然可信。
- 不要把 Vault token、密码写入插件 command args。
- 使用凭证管理器自身支持的登录、授权和密钥存储机制。
- 不要把密码作为普通命令行参数传递；命令行参数可能被进程列表、日志或诊断输出暴露。
- 对 MFA、验证码和高风险登录流程，应保留人工确认和接管能力。

## 9. MCP 集成详解

### 9.1 MCP 在这里做什么

MCP 将 agent-browser 的浏览器能力包装为结构化工具。Agent 宿主不必自己拼接 shell 命令，而是可以调用类似 open、snapshot、click、fill 的工具。

典型调用链：

```text
MCP Host（例如支持本地 MCP 的 Agent 客户端）
          |
          | 启动子进程并通过 stdio 通信
          v
agent-browser mcp
          |
          v
agent-browser daemon
          |
          v
浏览器
```

### 9.2 启动方式

```powershell
agent-browser mcp
```

MCP Server 使用 stdio：宿主启动子进程，通过标准输入/输出交换 MCP JSON-RPC 消息。它不是默认监听公网端口的 HTTP 服务。

### 9.3 通用客户端配置示例

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

这是通用配置形态，不是所有宿主都使用同一个配置文件或字段。应按具体客户端的 MCP 配置规范填写。

如果宿主找不到全局命令：

1. 先在 PowerShell 执行 `Get-Command agent-browser` 查找可执行文件。
2. 确认 Agent 宿主运行环境能访问该路径。
3. 在宿主配置中使用绝对路径，或使用宿主支持的 Node/npm 启动方式。
4. 检查宿主是否继承正确的 PATH 和环境变量。

### 9.4 MCP 工具集 Profile

官方命令文档提供不同工具 profile。常见形式：

```powershell
agent-browser mcp
agent-browser mcp --tools all
agent-browser mcp --tools core,network,react
```

官方文档列出的 profile 类别包括：

- `core`：日常浏览器导航、快照、交互、等待、读取、截图和基础标签页能力。
- `network`：网络路由、请求检查、HAR、headers、credentials、offline。
- `state`：Cookie、Storage、认证、保存状态、session、profile、skills。
- `debug`：console/errors、trace、profiling、recording、a11y、clipboard、plugins、doctor 等。
- `tabs`：前进/后退/刷新、tabs、windows、frames、dialogs。
- `react`：React tree/inspect/renders/suspense、vitals、pushstate。
- `mobile`：viewport、device、geolocation、media、touch、swipe、mouse、keyboard。
- `all`：完整工具面。

默认 profile 以当前版本文档为准；官方当前命令参考说明默认是 `core`。工具越多，Agent 可用能力越广，但工具发现和权限审核成本也越高。建议从 core 开始，确有需求再扩展。

### 9.5 MCP 接入排错

| 现象 | 优先检查 |
|---|---|
| 宿主提示 command not found | 可执行文件路径、PATH、宿主进程环境 |
| MCP 进程启动后立即退出 | 命令/参数是否正确、Node/npm 环境、stderr 日志 |
| MCP 能连接但没有需要的工具 | tools profile 是否包含对应能力 |
| 工具能调用但浏览器打不开 | Chrome 是否安装、daemon 状态、网络、代理 |
| 会话互相干扰 | 是否显式区分 session |
| 工具调用超时 | 页面等待策略、默认 timeout、浏览器是否卡住 |
| Windows 下配置不生效 | JSON 格式、反斜杠转义、宿主是否使用预期配置文件 |

### 9.6 ChatGPT 接入边界

必须区分三件事：

- 项目有 MCP Server。
- 某个 Agent 客户端支持 MCP。
- 当前 ChatGPT 账号/工作区具有相应连接入口，并支持该服务使用的传输方式。

这三件事不能互相推导。

agent-browser 的上述启动方式是本地 stdio MCP。只有宿主能启动本地 MCP 子进程时，才能直接采用该配置。若某宿主只接受远程 MCP，就必须另行部署可访问的远程适配服务，并实现认证、授权、网络隔离、会话隔离和审计；这不是简单把 `agent-browser mcp` 放到公网即可完成的。

## 10. Skills：让 Agent 知道如何正确使用

### 10.1 Skill 与 CLI/MCP 的分工

- CLI 是执行入口。
- MCP 是工具协议接入层。
- Skill 是给 Agent 的操作说明和工作流知识。

Skill 本身不会替代浏览器，也不会因为安装 Skill 就自动获得浏览器执行权限。宿主还必须能调用 CLI 或 MCP。

### 10.2 安装发现 Skill

官方推荐通过 skills.sh 安装：

```powershell
npx skills add vercel-labs/agent-browser
```

官方说明该安装入口是一个较薄的 discovery skill，告诉 Agent 何时使用 agent-browser，以及如何通过 CLI 获取当前版本对应的详细指引。

### 10.3 从 CLI 获取版本匹配的 Skill 内容

```powershell
agent-browser skills list
agent-browser skills get core
agent-browser skills get core --full
agent-browser skills get core --ref commands
agent-browser skills path core
```

这里有一个重要设计：实际命令参考由本机安装版本的 CLI 提供，避免 Agent 长期使用一份过时的静态命令文档。

### 10.4 Skill 是否会自动调用

是否自动调用取决于宿主的 Skill 机制、触发描述和当前任务。安装 Skill 不等于所有对话都会调用它，也不等于宿主必然允许执行命令。实际使用时应检查宿主的 Skill 列表、触发记录和工具权限。

## 11. 安全模型与风险

### 11.1 先理解默认状态

**官方 Security 文档明确说明：安全功能是 opt-in（选择启用）的。默认情况下，agent-browser 不对导航、动作和输出施加这些额外限制。**

因此，不能因为工具提供安全功能，就认为新安装后已经受到完整保护。

### 11.2 主要风险

1. **Prompt Injection**：网页可能包含伪装成系统指令或工具结果的恶意内容。
2. **凭证泄露**：浏览器登录态、Cookie、Storage、下载文件可能包含敏感信息。
3. **越权操作**：Agent 可能点击删除、发布、付款、发送消息等高影响按钮。
4. **域名外访问**：自动化流程可能被重定向到非预期网站。
5. **插件供应链**：本地插件以本地进程运行，不能把插件当作沙箱。
6. **输出污染**：大段网页内容可能混入 Agent 上下文，造成误判或指令混淆。
7. **并发会话混淆**：多个任务共享浏览器状态会导致数据串用。

### 11.3 建议启用的防护层

| 防护 | 目的 | 注意事项 |
|---|---|---|
| allowedDomains | 限制可访问域名 | 不是 OS 网络防火墙；具体限制依运行模式而定 |
| actionPolicy | 限制可执行动作 | 策略文件应经过审查和测试 |
| confirmActions | 对敏感动作要求确认 | 确认类别与行为需按当前版本核验 |
| confirmInteractive | 交互式确认 | 非交互环境可能无法按预期确认 |
| contentBoundaries | 标记网页不可信内容边界 | 不能消除 Prompt Injection，只是辅助模型区分内容 |
| maxOutput | 限制输出长度 | 防止超长页面内容占用上下文 |
| session 隔离 | 隔离浏览器状态 | 不同信任级别的任务不要共享会话 |
| 最小权限账户 | 限制业务影响 | 比单靠提示词更可靠 |

### 11.4 安全配置建议

先在专用测试环境中验证以下配置项，再应用于真实任务：

```json
{
  "contentBoundaries": true,
  "maxOutput": 50000,
  "allowedDomains": [
    "localhost",
    "127.0.0.1",
    "your-test-domain.example"
  ],
  "actionPolicy": "./policy.json"
}
```

这只是配置结构示例，不是完整的生产安全策略。域名白名单的具体语义、策略 JSON 的 schema、确认动作类别和不同 Provider 的支持情况必须查阅当前版本 Security 文档。

**额外边界：**

- 域名白名单不等于网络层防火墙。
- 不要让浏览器自动化进程拥有不必要的文件系统、网络和账户权限。
- 对支付、删除、发布、发送等动作，应要求人工确认。
- 对不可信网站，优先使用无敏感登录态的独立浏览器 session。
- 不要把密码、Vault token 或 API Key 放在命令参数、仓库或普通日志中。
- 生产部署应考虑容器/虚拟机隔离、网络出口限制、凭证管理和审计。

## 12. 典型应用场景与选型建议

### 12.1 网页信息提取

适合需要读取真实渲染内容、展开页面组件、处理前端路由的网站。若内容可通过官方 API 或静态页面直接获取，优先用更轻量的 API/HTTP 方式。

### 12.2 表单和后台流程

适合重复性、低风险、可验证的表单录入或后台操作。涉及发送、删除、付款、权限变更等动作时，应加入人工确认和结果核验。

### 12.3 Web 应用端到端验收

适合从真实浏览器角度检查：

- 页面能否打开。
- 导航是否正确。
- 表单能否输入和提交。
- 关键交互是否正常。
- 页面是否出现错误提示。
- 截图是否符合预期。

如果要建立完整、长期的自动化测试体系，仍需考虑测试断言、测试数据管理、CI 集成和报告能力；agent-browser 可作为执行工具，但不能自动替代完整测试工程。

### 12.4 GitHub Pages 在线工具平台

对于本仓库正在研究的 Vue 3、无 TypeScript、GitHub Pages 在线工具平台，agent-browser 的价值主要在**部署后页面验收**：

1. 打开 GitHub Pages 地址。
2. 检查首屏和路由。
3. 遍历工具页面和主要交互。
4. 检查控制台错误、页面异常和资源加载。
5. 截图留存，作为版本验收证据。

但 GitHub 仓库创建、文件提交、Issue/PR 管理等结构化任务，优先使用 GitHub API 或 GitHub 连接器。不要把浏览器模拟操作作为所有 GitHub 管理任务的默认方案。

## 13. 常见问题与排错路径

### 13.1 命令找不到

```powershell
Get-Command agent-browser
npm config get prefix
npm root -g
```

检查全局安装是否成功、npm 全局 bin 是否在 PATH 中，以及当前终端是否在安装后重新打开。

### 13.2 浏览器未安装或启动失败

```powershell
agent-browser doctor
agent-browser install
```

先看 doctor 输出中 Chrome 路径、版本、缓存和启动测试结果。若网络无法访问 Chrome for Testing 下载源，检查代理和网络策略。

### 13.3 refs 失效或点击错误

重新执行：

```powershell
agent-browser snapshot -i
```

不要继续使用导航前或 DOM 更新前的 ref。优先确认当前页面和目标元素，再操作。

### 13.4 页面加载慢或命令超时

- 检查目标站点是否正常。
- 使用等待条件而不是任意增加固定 sleep。
- 确认是否遇到登录、验证码、弹窗或重定向。
- 检查默认超时配置；官方配置文档提示默认 timeout 应低于 IPC timeout 上限。
- 必要时使用 `doctor`、console/errors、trace 等诊断能力。

### 13.5 MCP 连接成功但工具不足

检查启动参数中的 `--tools` profile。先用默认 core；只有需要 network、state、debug、tabs、mobile 等额外能力时才扩展。

### 13.6 浏览器状态互相影响

为不同任务指定不同 session，不要共享同一登录态。检查是否通过环境变量或宿主参数意外复用了 session 名称。

### 13.7 代理环境

如果目标站点需要代理，检查工具配置、系统代理和浏览器启动参数是否一致。代理凭证不要写入仓库或公开日志；证书错误不应通过无条件忽略 HTTPS 错误来“解决”，除非仅在受控测试环境且理解风险。

## 14. 维护状态、许可证与版本管理

### 14.1 版本记录

本档案以 2026-11-10 发布的 v0.42.0 Release 为核验基线，核验日期为 2026-11-12。该版本 Release 涉及浏览器会话复用、Safari/iOS、MCP、文本输入和 Windows 录制等修复。

由于上游持续迭代，使用者应：

- 在实际安装后记录 `agent-browser --version`。
- 将版本号和配置文件纳入项目环境记录。
- 升级前检查 Release Notes。
- 对 MCP 工具 profile、参数和 Skill 内容重新验证。
- 生产自动化不要在未测试的情况下无条件追踪最新版本。

### 14.2 许可证

上游仓库标注 Apache-2.0。使用、修改或再分发时，应以仓库当前 LICENSE 和第三方依赖许可为准，保留要求的版权与许可证声明。许可证允许使用不代表目标网站允许自动化访问；还需遵守网站条款、隐私和数据保护要求。

## 15. 综合评估

| 评估维度 | 结论 | 说明 |
|---|---|---|
| 实际使用价值 | 高 | Agent 浏览器操作、网页验收和自动化均有明确用途 |
| 独特性 | 中高 | CLI + daemon + snapshot refs + MCP/Skill 组合有较强 Agent 使用导向 |
| 成熟度 | 较高，仍快速迭代 | 功能面较广，但版本变化需要持续跟踪 |
| 维护状况 | 活跃 | 核验基线版本近期发布 |
| 可用性 | 较好 | npm 安装路径直接；首次浏览器下载受网络影响 |
| 安装复杂度 | 低至中 | 基础安装简单；代理、会话、安全和 MCP 配置增加复杂度 |
| 安全与权限 | 需主动治理 | 安全功能 opt-in，浏览器本身具备较高访问能力 |
| 许可证 | 友好 | Apache-2.0，仍需核验依赖和再分发义务 |
| 长期参考价值 | 高 | 可作为 Agent 工具接口、浏览器自动化和安全边界的研究样本 |
| 与本仓库重复度 | 低 | 属于通用浏览器自动化底座，与现有档案方向互补 |

**最终结论：推荐收录。** 建议实际试用，但应从公开网页、只读任务和专用 session 开始，再逐步增加交互权限。

## 16. 插件/工具封装可行性

### 16.1 适合的形态

| 形态 | 适配度 | 判断 |
|---|---|---|
| 原生 CLI | 很高 | 上游已经提供，优先直接使用 |
| 本地 MCP | 很高 | 上游已经提供，适合支持本地 stdio MCP 的 Agent |
| Skill | 很高 | 官方已有发现 Skill 和运行时文档机制 |
| 远程 MCP / 受控浏览器服务 | 中 | 可做，但需要额外部署、鉴权、隔离和审计 |
| ChatGPT 专用插件/应用 | 当前条件下不确定 | 取决于账号/宿主连接能力及可用传输方式，不能仅凭项目有 MCP 就承诺可接入 |
| Fork 上游 | 低 | 没有明确缺口时不建议承担维护分叉成本 |

### 16.2 推荐接入顺序

1. **先在本地 Agent 客户端试用 CLI。** 验证基础命令和浏览器环境。
2. **再接入本地 MCP。** 从 core profile 开始，确认宿主能启动本地子进程。
3. **安装 Skill。** 让 Agent 获得与本机版本匹配的命令说明。
4. **建立最小权限配置。** 域名白名单、动作策略、内容边界、输出限制、独立 session。
5. **最后评估远程服务。** 只有确实需要云端、多用户或 ChatGPT 等远程宿主时才考虑。

### 16.3 如果未来开发远程适配层

不要简单把本地 MCP 进程暴露到公网。至少要设计：

- 身份认证与细粒度授权。
- 每个用户/任务独立浏览器 session。
- 域名和网络出口限制。
- 敏感动作人工确认。
- 文件下载/上传隔离和大小限制。
- 凭证托管，不向模型暴露明文密码。
- 审计日志、超时、并发限制和资源回收。
- 服务端与浏览器运行环境隔离。

远程适配层扩大了浏览器可访问范围，风险和运维成本明显高于本地 CLI/MCP。

## 17. 后续实践路线

建议按以下顺序验证，不要一开始就接入高权限账号：

- [ ] 在 Windows 安装 CLI 和 Chrome for Testing。
- [ ] 执行 `agent-browser doctor`，保存诊断结果。
- [ ] 用公开网页练习 open、read、snapshot、click、fill、wait、screenshot。
- [ ] 测试导航和 DOM 变化后重新获取 refs 的必要性。
- [ ] 测试独立 session，确认状态隔离。
- [ ] 在支持本地 MCP 的 Agent 客户端配置 stdio MCP。
- [ ] 安装官方 Skill，检查 Agent 是否能正确识别和使用。
- [ ] 在专用测试站点验证 allowedDomains、actionPolicy 和 confirmActions。
- [ ] 将其用于 Vue/GitHub Pages 项目的只读页面验收。
- [ ] 完成实际验证后，把实测系统、版本、命令输出和问题补充到本档案。

## 18. 官方资料与证据边界

详细链接见同目录 [references.md](./references.md)。

本文内容按以下证据类型理解：

- **官方资料确认**：项目功能、安装方式、配置优先级、CLI/MCP/Skill 入口、默认安全功能状态等，依据上游 GitHub、Release 和官方文档。
- **工程解释**：CLI/daemon/CDP 分层、与 Playwright/API 的选型比较、典型架构和应用场景，是对官方能力的工程化解释。
- **实践建议**：Windows 非系统盘规划、最小权限、独立 session、GitHub Pages 验收流程等，是结合使用场景提出的建议，不代表已在用户机器上实测。
- **尚待实测**：具体 Windows 安装路径、不同 Agent 宿主配置、各 Provider 的功能差异、策略文件对特定动作的实际拦截效果。需要在目标环境中验证后才能标记为“已测试”。

---

**档案维护规则：** 上游版本、安装命令、MCP schema、安全策略或 Windows 支持发生变化时更新本文和 references.md；每次更新记录核验日期，并将官方确认、实际测试和分析判断分开记录。
