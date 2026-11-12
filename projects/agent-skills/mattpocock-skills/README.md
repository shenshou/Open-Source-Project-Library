# mattpocock/skills

> 面向真实软件工程工作的 Agent Skills 集合：把需求澄清、领域建模、规格制定、任务拆分、编码实现、测试、调试、代码审查和工程维护组织成可组合的工作流。

- **收录状态**：recommended（推荐收录）
- **项目类型**：AI Agent Skills / 软件工程工作流 / 开发者工具
- **AI 项目标记**：是（Agent 工作流与指令集合，不是 AI 模型）
- **上游仓库**：[mattpocock/skills](https://github.com/mattpocock/skills)
- **项目主页**：[aihero.dev/skills](https://aihero.dev/skills)
- **技能目录**：[skills/](https://github.com/mattpocock/skills/tree/main/skills)
- **许可证**：MIT
- **本档案核验的最新 Release**：v1.3.2（2026-10-29）
- **上游主分支核验时间**：2026-11-13（UTC+8）
- **核验时仓库状态**：公开、未归档；GitHub API 显示约 322,767 Stars、27,069 Forks（动态数据，仅作当时参考）
- **文档状态**：研究档案；Release 版本与 main 分支内容分开记录
- **实测状态**：已核验仓库结构、README、插件清单、技能目录、最新 Release 和许可证；未声称已在用户本机或每一种 Agent 宿主上完成安装实测

---

## 0. 结论先行

**结论：推荐正式收录。** 这是一个有较高工程实践参考价值的 Agent Skills 集合。它的核心价值不是“让 AI 自动写代码”，而是把软件工程中容易被 Agent 跳过或做浅的工作，组织成有入口、有阶段、有产物、有完成条件的可复用流程。

项目主要解决几类常见问题：

1. **需求理解偏差**：通过 grilling（结构化追问）把模糊想法澄清为可执行需求。
2. **缺少共同语言**：通过领域建模、Glossary 和 ADR，减少术语混乱和决策丢失。
3. **实现过程失控**：通过 Spec、任务图、阻塞关系和分阶段实现，让大任务可拆解、可追踪。
4. **代码未经验证**：通过 TDD、调试反馈环和双维度 Code Review，要求实现有测试证据。
5. **代码库逐渐失去结构**：通过架构改进调查、深模块设计和 Retro，持续改善 Agent 的工作环境。
6. **长任务跨会话丢失上下文**：通过 Handoff、Wayfinder 和持久化文档，让工作能够交接和延续。

它不是 GSD、BMAD 或 Spec-Kit 的完整替代品，也不是一个会自己运行的自治软件开发平台。它提供的是一组可组合的 Agent 工作方法；真正执行能力取决于宿主 Agent 是否支持 Skill 调用、子 Agent、后台任务、Worktree、终端和对应的 Issue Tracker。

**对本项目库的价值：** 值得作为 Agent Skills 与软件工程工作流的代表项目长期跟踪；其中的需求澄清、任务拆分、TDD、代码审查、上下文管理和文档组织方法，也可以作为其他项目封装 Skill 时的设计参考。

## 1. 项目定位与边界

### 1.1 它是什么

作者 Matt Pocock 将自己在真实工程开发中使用的 Agent Skills 开源。仓库 README 将其定位为面向真实工程实践的技能集合，强调小型、可调整、可组合，并希望能在不同模型/Agent 环境中使用。

Skill 通常由一个 `SKILL.md` 及其可选参考文件组成。它不是模型权重，也不等于可执行程序。它主要通过自然语言指令、流程约束、上下文引用和宿主提供的工具，引导 Agent 按特定方法工作。

### 1.2 它不是什么

- **不是 AI 模型**：不提供推理模型、训练或推理服务。
- **不是完整 IDE 或 Agent Runtime**：不负责提供代码编辑器、模型调用、终端、浏览器或子 Agent 运行时。
- **不是一个单体、不可拆分的开发框架**：多数技能可以按需安装和组合。
- **不是自动保证正确性的质量门禁**：Skill 可以要求测试和审查，但是否真正运行、结果是否可信，仍取决于 Agent、项目工具链和执行环境。
- **不是 ChatGPT 网页版插件**：仓库包含 Agent Skills 和 Claude Code 插件分发配置，也提供 Codex 等宿主的安装说明；不能把它理解成可直接安装到 ChatGPT 网页对话中的插件。

### 1.3 设计取向

作者对 GSD、BMAD、Spec-Kit 等“由流程框架接管项目过程”的方案提出了不同取向：尽量保留开发者控制权，把工作方法拆成小型 Skill，由用户或 Agent 在合适阶段组合，而不是强制所有项目走一套封闭流程。

这是一种设计立场，不意味着其他框架一定会失去控制权；实际体验仍需结合各方案的工作流、可配置程度和团队习惯判断。

## 2. 功能全景与技能清单

截至核验时，插件清单 `.claude-plugin/plugin.json` 列出 **27 个正式随插件分发的 Skills**：20 个 Engineering、7 个 Productivity。仓库还存在不在该插件清单中的 Misc、In Progress 等目录；不能把整个仓库的全部目录都当作稳定插件内容。

### 2.1 Engineering：软件工程流程

| Skill | 调用类型 | 核心用途 |
|---|---|---|
| `ask-matt` | 用户主动调用 | 技能路由器，帮助选择合适的流程 |
| `grill-with-docs` | 用户主动调用 | 通过访谈澄清想法，同时维护领域词汇和 ADR |
| `triage` | 用户主动调用 | 将外部 Issue / Bug / 功能请求按角色和状态整理为可执行事项 |
| `improve-codebase-architecture` | 用户主动调用 | 调查代码库中的深模块化和架构改进机会 |
| `setup-matt-pocock-skills` | 用户主动调用 | 为当前仓库配置 Issue Tracker、Triage 标签和领域文档布局 |
| `to-spec` | 用户主动调用 | 将讨论内容整理为 Spec |
| `to-tickets` | 用户主动调用 | 将 Spec 拆为带阻塞关系的 tracer-bullet tickets |
| `implement` | 用户主动调用 | 根据 Spec 或 Ticket 实现一项工作，并调用 TDD 与 Code Review |
| `implement-spec` | 用户主动调用 | 将整份 Spec 作为任务图，在集成分支上并行实现多个 Ticket |
| `wayfinder` | 用户主动调用 | 为超大、尚不清晰的工作建立决策地图，逐步消除不确定性 |
| `retro` | 用户主动调用 | 复盘 Agent 工作环境，提出工具、规则、导航和质量检查改进 |
| `prototype` | 模型/用户可调用 | 通过一次性原型回答 UI、状态或业务逻辑问题 |
| `diagnosing-bugs` | 模型/用户可调用 | 对难复现问题建立反馈环、缩小范围、定位并回归测试 |
| `research` | 模型/用户可调用 | 基于一手资料研究主题并保存带引用的 Markdown 结果 |
| `tdd` | 模型/用户可调用 | 采用 Red-Green-Refactor 进行小步测试驱动实现 |
| `domain-modeling` | 模型/用户可调用 | 建立并维护领域术语、Glossary 和 ADR |
| `codebase-design` | 模型/用户可调用 | 提供深模块、接口、边界和可测试设计的共同语言 |
| `code-review` | 模型/用户可调用 | 从 Standards 与 Spec 两个维度审查变更 |
| `pr` | 模型/用户可调用 | 组织 Pull Request 描述、前后证据和合并风险 |
| `wizard` | 模型/用户可调用 | 为必须由人操作的配置、凭证、基础设施或迁移任务生成交互式向导 |

> 调用类型是上游文档中的设计分类。不同宿主对自动调用开关的实现方式不同，不能假设所有平台行为完全一致。

### 2.2 Productivity：通用协作流程

| Skill | 调用类型 | 核心用途 |
|---|---|---|
| `grill-me` | 用户主动调用 | 对计划或设计进行结构化追问，不依赖仓库持久化文档 |
| `grilling` | 模型/用户可调用 | 提供访谈式澄清的底层通用流程 |
| `handoff` | 用户主动调用 | 把当前会话整理成新 Agent / 新会话可接手的交接文档 |
| `teach` | 用户主动调用 | 在当前工作目录中开展多阶段、带状态的教学 |
| `to-questionnaire` | 用户主动调用 | 将需要第三方决策的问题整理为问卷 |
| `wait-what` | 用户主动调用 | 当用户没有理解 Agent 的解释时，换一种更直白的方式重讲 |
| `writing-for-agents` | 模型/用户可调用 | 指导编写 Skill、AGENTS.md、CLAUDE.md 等 Agent 消费的文档 |

### 2.3 非插件稳定目录

- **Misc**：例如 Claude Code Git 操作防护 Hook、迁移测试写法、搭建 Pre-commit 等。作者说明这些工具保留在仓库中，但不作为主要插件内容推广。
- **In Progress**：实验性 / Beta 技能，作者明确说明它们不包含在插件和顶层稳定技能列表中，可能改变或消失。需要单独安装，不应当作稳定能力。
- **Deprecated**：历史归档内容，不应作为当前推荐入口。

正式使用时，优先以当前插件清单和稳定目录为准；不要仅凭仓库中存在某个文件，就判断它属于正式插件包。

## 3. 核心工作流：从想法到交付

项目的关键不是单个 Skill，而是这些 Skill 之间的路由和衔接。其主线可概括为：

```text
想法 / 需求
   |
   v
grill-with-docs
澄清需求、建立领域语言和 ADR
   |
   +---- 需要可运行证据？ ----> handoff -> prototype -> handoff
   |
   v
判断工作规模
   |
   +---- 单项工作 --------------------+
   |                                  |
   +---- 多阶段工作 -> to-spec        |
                       |              |
                    to-tickets        |
                       |              |
             +---------+---------+    |
             |                   |    |
          implement          implement-spec
             |                   |
             +---------+---------+
                       |
                TDD / 回归测试
                       |
                 code-review
                       |
                    PR / 交付
                       |
                     retro
                       |
                 改善下一轮环境
```

### 3.1 需求澄清：Grilling

`grill-with-docs` 不是简单地让 Agent “问几个问题”。它把需求澄清视为一个需要逐步消除歧义的过程，并将稳定下来的术语、领域模型和难以逆转的决策沉淀到 `GLOSSARY.md` 与 ADR。

没有工作目录、只想讨论一个计划时，可以使用 `grill-me`；有代码仓库并希望长期保留知识时，优先使用 `grill-with-docs`。

### 3.2 Spec 与 Tickets

- `to-spec`：把已经讨论清楚的目标、范围、约束和验收条件整理成 Spec。
- `to-tickets`：将 Spec 拆成较小的 tracer-bullet tickets。每个 Ticket 不只是步骤列表，还要声明阻塞关系。
- 这些 Ticket 应足够自包含，让后续实现 Agent 能在较新的上下文中完成工作。

### 3.3 两种实现路径

**路径 A：逐 Ticket 实现**

`implement` 逐个处理 Ticket，适合用户希望控制每个小任务、按阻塞顺序推进的情况。上游流程要求在约定的接口边界调用 TDD，完成后调用 Code Review。

**路径 B：整份 Spec 并行实现**

`implement-spec` 将 Ticket 视为任务图，寻找当前已解除阻塞的 frontier，在集成分支上为每个 Ticket 分配独立 worktree / branch 和新鲜上下文的实现子 Agent。完成一批任务后再推进依赖它们的任务，最终对集成分支进行 Code Review。

并行路径对宿主要求明显更高：需要可靠的子 Agent、后台执行、Git worktree、分支管理和冲突整合能力。若宿主没有这些能力，不能仅靠安装 Skill 就获得并行开发运行时。

### 3.4 反馈环与质量控制

- `tdd`：先建立失败测试，再实现最小改动使测试通过，最后重构。
- `diagnosing-bugs`：面对难定位 Bug，先建立能稳定复现该问题的红色反馈环，再缩小范围、提出假设、插桩、修复并增加回归测试。
- `code-review`：分开检查工程标准（Standards）和是否符合原始 Spec（Spec），避免只看代码风格而漏掉需求偏差。
- `retro`：将本次工作中暴露的问题反馈到 Agent 环境，如导航文档、自动检查、编码规范和工具配置。

这些流程能提高执行纪律，但不能代替真实测试、代码审查者判断、生产监控或安全评估。

## 4. 关键机制与架构

### 4.1 Skill 文件是流程说明，不是独立执行引擎

一个 Skill 目录通常包含：
- `SKILL.md`：名称、描述、调用策略和主要步骤。
- 可选参考文件：模板、领域规则、示例、详细操作说明。
- 有些流程会引用其他 Skill，由宿主的 Skill 调用能力执行衔接。

因此，Skill 的行为由三部分共同决定：**Skill 文档 + Agent 模型的理解与执行 + 宿主提供的工具能力**。

### 4.2 用户主动调用与模型自动调用

仓库将技能分为两类：

- **User-invoked**：用户明确输入命令或技能名称后才启动，适合会改变工作阶段、需要用户决策或执行成本较高的流程。
- **Model-invoked**：当任务描述符合触发条件时，Agent 可以主动调用，也允许用户直接调用，适合可复用的底层方法或质量检查。

例如 `ask-matt` 是用户主动调用的路由器；`tdd`、`code-review`、`domain-modeling` 等可以在合适的工作中由 Agent 主动调用。具体的 frontmatter 和宿主配置决定其可见性和隐式调用策略。

### 4.3 渐进式披露（Progressive Disclosure）

技能不会把所有细节都塞进一个超长提示。通常先暴露短描述和主流程，需要某个分支时再读取对应参考文件。这样能减少每轮上下文负担，也让复杂流程按需展开。

仓库中的 `writing-for-agents` 对这一设计有较系统的解释：上下文负担（Context Load）与人类认知负担（Cognitive Load）需要权衡；步骤、参考资料和外置文档应放在不同层级。

### 4.4 路由器与技能组合

`ask-matt` 将多个独立 Skill 组织为“主流程 + 入口 + 底层词汇层”：
- 主流程：grill → spec → tickets → implement → TDD / review → retro。
- Bug / Issue 入口：triage 或 diagnosing-bugs。
- 大型模糊工作入口：wayfinder。
- 底层设计语言：domain-modeling 与 codebase-design。

这让项目不是简单堆积命令，而是形成有使用时机和边界的技能网络。

## 5. 安装与宿主兼容性

上游 README 当前提供多种安装路线。**同一个 Agent 环境通常只应选择一种分发路线**，避免插件包与手动复制的 Skill 重复出现。

### 5.1 Claude Code：官方插件市场

```bash
claude plugin install mattpocock-skills@claude-plugins-official
```

这是托管、只读的插件安装路线。README 表示官方市场会同步作者发布的版本；如果要直接跟踪作者自己的 Marketplace，可按 README 指引添加 `mattpocock/skills` 市场，并在 Claude Code 的 Marketplace 设置中启用自动更新。第三方市场的更新开关可能默认关闭，实际以当前 Claude Code 版本为准。

### 5.2 Codex：插件市场路线

上游 README 当前给出的命令：

```bash
codex plugin marketplace add mattpocock/skills
codex plugin add mattpocock-skills@mattpocock
```

README 表示 Codex 插件会在启动时更新。该行为依赖 Codex 当前版本的插件机制和市场配置；安装前应核对本机 Codex 版本与官方插件文档。

### 5.3 GitHub Copilot CLI / VS Code

README 给出的 Copilot CLI 路线：

```bash
copilot plugin marketplace add mattpocock/skills
copilot plugin install mattpocock-skills@mattpocock
```

并要求在 `~/.copilot/settings.json` 中配置额外 Marketplace 与 `autoUpdate`。VS Code 则提供从来源安装插件的入口，README 标注其按日更新。Copilot CLI 与 VS Code 的功能成熟度和命令可能随版本变化，须以宿主当前文档为准。

### 5.4 Gemini CLI

README 提供按目录安装 Engineering 与 Productivity 两组技能的方式：

```bash
gemini skills install https://github.com/mattpocock/skills.git --path skills/engineering
gemini skills install https://github.com/mattpocock/skills.git --path skills/productivity
```

重新执行安装命令以获取更新。不要将此路线误认为 Claude Code 插件安装。

### 5.5 通用 Agent / 可编辑 Skill 文件

通过 Skills CLI：

```bash
npx skills@latest add mattpocock/skills
```

按交互提示选择 Agent 和需要安装的 Skills，并建议包含 `setup-matt-pocock-skills`。如果需要明确指定 Agent，可按当前 CLI 帮助使用 `-a` 参数。

更新已有安装：

```bash
npx skills@latest update
```

如果要获取新增技能或重新选择安装集合，再执行 `add`。这种路线会把可编辑文件复制到项目或 Agent 约定目录，用户拥有文件的修改权；不会像托管插件那样在后台自动同步。

### 5.6 安装路线对比

| 路线 | 文件管理方式 | 更新模式 | 适合情况 |
|---|---|---|---|
| Claude Code 官方插件市场 | 托管、只读插件包 | 市场发布后自动更新（有同步延迟） | 希望少维护、接受托管版本 |
| Claude Code 作者 Marketplace | 托管插件包 | 可启用自动更新；第三方市场可能默认关闭 | 希望更直接跟踪上游 |
| Codex 插件市场 | 托管插件 | README 标注启动时更新 | 使用 Codex 插件体系 |
| Copilot 插件 | 托管插件 | 可配置自动更新 | 使用 Copilot CLI / VS Code |
| Gemini CLI Skills | 安装到宿主的技能目录 | 重新运行安装命令 | 使用 Gemini CLI |
| Skills CLI | 复制为可编辑文件 | 手动运行 update / add | 希望自己修改、审查和控制更新 |

## 6. 每个仓库的一次性初始化

安装技能后，上游建议在目标代码仓库中运行：

```text
/setup-matt-pocock-skills
```

它不是一个确定性安装脚本，而是一个 prompt-driven Skill：先检查仓库，再向用户展示发现结果和配置草案，确认后写入。

### 6.1 它会检查什么

- Git Remote，判断当前仓库属于 GitHub、GitLab 或无远程仓库。
- 根目录是否已有 `AGENTS.md` 或 `CLAUDE.md`。
- 是否已有 `GLOSSARY.md`、Glossary Map、ADR 文档和旧版 `CONTEXT.md`。
- 是否已存在 `docs/agents/` 配置。
- 是否采用本地 Markdown Issue Tracker。
- 是否安装 Triage Skill，以决定是否需要配置 Triage 标签。
- 是否为 Monorepo，以决定领域文档采用单上下文还是多上下文布局。

### 6.2 可能生成的文件

典型输出包括：
- `docs/agents/issue-tracker.md`：Issue Tracker 的访问和操作约定。
- `docs/agents/domain.md`：领域文档位置及读取规则。
- `docs/agents/triage-labels.md`：仅在安装 Triage 且需要时生成。
- 在现有 `AGENTS.md` 或 `CLAUDE.md` 中加入 `## Agent skills` 区块。

它明确要求先探索、展示草案并让用户确认，再写入；如果根目录没有 AGENTS.md 或 CLAUDE.md，不会擅自替用户选择创建哪一个。

**重要：** 这个 Skill 会根据仓库情况读取并修改文档，运行时应检查草案和目标路径。不要在不相关的仓库里盲目运行，也不要把它当作只读诊断命令。

## 7. 从零开始的建议使用流程

以下是一种适合真实代码项目的使用路径。命令名称来自上游技能定义，是否可调用取决于宿主是否已正确安装。

### 场景 A：开发一个中等规模功能

1. 在项目目录打开 Agent，先调用 `/setup-matt-pocock-skills`（每个仓库初始化一次）。
2. 调用 `/grill-with-docs`，说明目标，让 Agent 追问边界、用户行为、数据结构和验收条件。
3. 调用 `/to-spec`，将当前讨论整理成 Spec。
4. 调用 `/to-tickets`，把 Spec 拆成独立 Ticket，并明确依赖关系。
5. 按 Ticket 调用 `/implement`。
6. 在实现过程中使用 `/tdd`，按小步 Red-Green-Refactor 推进。
7. 完成后使用 `/code-review`，分别检查代码标准和 Spec 一致性。
8. 根据需要组织 PR，最后调用 `/retro` 复盘工作环境。

### 场景 B：修复复杂 Bug

1. 调用 `/diagnosing-bugs`。
2. 先建立稳定复现该 Bug 的测试或命令。
3. 缩小问题范围，形成假设并用日志、插桩或最小化复现验证。
4. 修复并增加回归测试。
5. 用 `/code-review` 检查变更，再用 `/retro` 识别环境层面的预防措施。

### 场景 C：大型、尚未想清楚的项目

1. 使用 `/wayfinder` 建立目标地图和决策 Ticket。
2. 按依赖关系逐一研究、原型验证或访谈，产出决策而不是提前开始大规模编码。
3. 路线清晰后，回到 `/to-spec` 将决策收敛成可实现的 Spec。
4. 再通过 `/to-tickets` 和 `/implement` 或 `/implement-spec` 执行。

### 场景 D：跨会话交接

调用 `/handoff`，生成供新会话使用的交接文档。Skill 要求将文件写入操作系统临时目录，而不是当前工作区，并避免重复已经存在于 Spec、ADR、Issue、Commit 等其他产物中的信息。

## 8. 外部依赖与宿主能力要求

这个项目的真实能力边界由 Skill 与 Agent Runtime 共同决定。

| 能力 / 工具 | 典型用途 | 是否所有宿主都有 |
|---|---|---|
| Skill 调用机制 | 触发和组合技能 | 否 |
| 终端 / Shell | 测试、Git、CLI 操作 | 否 |
| Git / Worktree | 分支隔离、并行实现 | 否 |
| 子 Agent / 后台 Agent | implement-spec、research 等 | 否 |
| GitHub CLI `gh` | GitHub Issue、PR、标签操作 | 否 |
| GitLab CLI `glab` | GitLab Issue / MR 操作 | 否 |
| Issue Tracker | Ticket、阻塞关系和任务状态 | 需要配置；可使用 GitHub、GitLab、本地 Markdown 或用户描述的其他方案 |
| 测试、Lint、类型检查 | TDD 和验证 | 由项目自身提供 |
| 浏览器 / UI 运行环境 | Prototype 或真实界面验证 | 不由本仓库提供 |

因此：
- 只安装 Skill 不会自动获得 GitHub Token、仓库权限或 Issue Tracker 权限。
- 如果要让 Skill 创建 Issue、PR、分支或提交代码，宿主工具必须具备相应认证和权限。
- `implement-spec` 的并行效率取决于宿主是否真正支持独立 worktree 和后台子 Agent。
- 没有测试命令的项目，不能因为调用了 TDD Skill 就凭空获得测试覆盖。
- 用户可以选择不使用 GitHub Issue，改用本地 Markdown；但需要正确配置对应的 Tracker 约定。

## 9. 安全、权限与风险

### 9.1 主要风险不是 Skill 文件本身，而是它驱动的工具行为

Skill 会指导 Agent 操作代码仓库、Issue Tracker、Git、终端和外部服务。风险取决于宿主给它的工具权限。

重点关注：
- **文件写入**：初始化配置、Spec、Glossary、ADR、Ticket 和代码修改。
- **Git 操作**：创建分支、Worktree、提交、PR；错误目标仓库或错误分支会产生真实影响。
- **外部系统写入**：创建或关闭 Issue、修改标签、更新 PR。
- **并行 Agent**：多个 Agent 可能修改共享文件或对同一任务理解不一致。
- **凭证暴露**：Issue Tracker Token、环境变量和项目机密不应写入交接文档或普通 Markdown。
- **指令注入**：来自 Issue、PR、代码注释或外部文档的内容应视为不可信输入，不应允许其覆盖用户和宿主的安全策略。

### 9.2 建议的权限策略

1. 首次安装后先阅读 `SKILL.md` 和相关参考文件，尤其是会写文件或操作 Issue 的技能。
2. 先在测试仓库或独立分支试用。
3. 对创建 Issue、PR、提交、推送、删除和批量修改等动作保留人工确认。
4. 为 Agent 提供最小必要权限，不要为了让某个 Skill 运行而授予整个账户的管理员权限。
5. 并行实现时明确集成分支、Worktree 和文件所有权；合并前检查 Diff。
6. 使用 `handoff` 时检查是否包含 Token、密码、个人信息或内部地址。
7. 把测试结果、代码审查和部署结果分开记录；不要把 Agent 自述当成执行证据。

### 9.3 项目自带的防护是否足够

仓库中有 Misc 技能 `git-guardrails-claude-code`，用于配置 Claude Code Hook 拦截部分危险 Git 命令。但它不属于主要插件清单，也不能替代宿主权限控制、分支保护、备份或人工审查。不要假设安装主插件就已经启用了这些 Git 防护。

## 10. 与相近方案的关系

| 方案 | 主要形态 | 侧重点 | 与本项目关系 |
|---|---|---|---|
| GSD | 软件开发工作流框架 | 结构化规划、阶段执行和上下文管理 | 同属工程流程类方案；本项目更强调小型 Skill 组合与用户控制 |
| BMAD | 多角色、阶段化方法体系 | 角色、流程和交付物 | 更偏完整方法体系；本项目更轻量、可拆分 |
| Spec-Kit | Spec 驱动开发工具链 | 规格定义与实现约束 | 可在概念层面对比；本项目提供从澄清到实现的更多技能组合 |
| 单独的 TDD / Code Review Skill | 单项能力 | 聚焦测试或审查 | 本项目将它们放进更大的工作流 |
| 自建 AGENTS.md / CLAUDE.md | 项目级指令 | 项目约定和规范 | 可与本项目互补，不是互斥关系 |

以上是定位层面对比，不是同一测试集下的性能评测。具体选择应看团队是否需要完整流程、可插拔程度、宿主能力和维护成本。

## 11. 适用场景与不适用场景

### 适合

- 使用 Claude Code、Codex、Copilot、Gemini CLI 或其他支持 Agent Skills 的编码 Agent。
- 经常开发中型以上软件项目，需要把需求、规格、任务、实现和验证串起来。
- 希望将领域知识、架构决策和项目约定沉淀到仓库。
- 有 GitHub / GitLab / 本地 Markdown 等 Issue 管理习惯。
- 想让 Agent 在编码前先澄清需求，编码时遵循 TDD，完成后进行系统审查。
- 多会话或多 Agent 开发，需要可追踪的交接和任务边界。

### 不太适合

- 只用 ChatGPT 网页版做日常问答、翻译、资料整理，不运行代码 Agent。
- 仅需一次很小的脚本修改，没有 Spec、Ticket 或测试流程成本。
- 宿主不支持 Skill、终端、Git 或必要的工具调用。
- 希望安装后完全无人监管地自动开发、提交和部署。
- 项目缺少测试基础设施，却期望仅靠 Skill 获得可靠质量保证。

### 对个人用户的使用建议

不要一次性启用全部流程。先从最能解决实际问题的几个能力开始：
1. `grill-me` / `grill-with-docs`：降低需求理解偏差。
2. `to-spec` + `to-tickets`：把较大的需求变成可执行工作。
3. `tdd`：建立测试反馈习惯。
4. `code-review`：让 Agent 在完成后检查自身变更。
5. `handoff`：解决跨会话上下文交接。

只有在项目确实需要并行实现、复杂 Issue 管理或长期架构维护时，再引入 `implement-spec`、`wayfinder`、`triage`、`improve-codebase-architecture` 等较重流程。

## 12. 对现有项目的参考价值

本项目可以为其他 Agent Skill 的设计提供若干可复用原则：

- **技能粒度**：一个 Skill 聚焦一个清晰职责，通过路由器组织，而不是把所有行为塞进单个超长文件。
- **显式调用边界**：高成本或需要用户参与的流程设为用户主动调用；可复用的底层方法才允许模型按需调用。
- **上下文渐进披露**：主文件保留步骤，分支细节外置到参考文件。
- **可检查的完成条件**：每个阶段应有可判断的完成标准，降低 Agent 过早结束的概率。
- **状态外置**：将决策、术语、任务和交接保存为可检索文档或 Tracker 记录，不依赖会话记忆。
- **反馈闭环**：测试、代码审查和 Retro 应形成连续过程。
- **宿主能力声明**：明确哪些功能依赖子 Agent、Worktree、Shell、Issue Tracker 或特定 Skill API。

这些原则适用于设计其他 Agent 工作流，但不意味着应照搬其全部命令、术语和流程。

## 13. 维护状态、版本和许可证

- **最新 Release（核验时）**：v1.3.2，发布于 2026-10-29。
- **主分支活动**：GitHub API 在 2026-11-13 核验时显示仓库最近仍有更新；主分支内容可能领先于最新正式 Release。
- **版本管理**：仓库包含 `CHANGELOG.md`、Changesets 配置和插件版本同步脚本；`package.json` 与插件 Manifest 的版本在核验时均为 1.3.2。
- **许可证**：MIT。若复制或分发 Skill 文件，应保留上游版权与许可证声明。
- **维护状态**：公开、未归档，Release 与主分支均有近期维护迹象。
- **Beta 管理**：In Progress 目录明确标注为实验性内容，可能随时变化或删除，且不进入主插件分发列表。

不要只看 Release 日期判断项目是否停止更新；应同时检查主分支提交、变更日志、插件清单和技能目录变化。

## 14. 插件 / Skill 封装与接入可行性

### 14.1 项目本身已经提供什么

它不是单纯的“可封装候选项目”：上游已经提供：
- Agent Skills 目录结构；
- Claude Code 插件 Manifest 与 Marketplace；
- Codex 插件安装路线；
- GitHub Copilot 插件安装路线；
- Gemini CLI 和通用 Skills CLI 安装方式。

因此，**优先直接使用上游发行物，不建议再复制一份内容相同的自制插件。**

### 14.2 对 ChatGPT 的边界

本项目的 Claude Code Plugin / Codex Plugin / Agent Skills 不能自动等同于 ChatGPT 网页版可安装的 Plugin。要在 ChatGPT 或其他宿主中使用，需要该宿主明确支持相应 Skill / 插件格式，或重新设计一层适配服务；仅有 `SKILL.md` 文件不意味着宿主会识别它。

### 14.3 自建封装的价值在哪里

只有在以下情况才值得二次封装：
- 需要将部分流程适配到一个上游未支持的 Agent 宿主；
- 希望按自身项目标准重写路由、权限和操作确认；
- 需要将通用流程与自己的工具、Issue Tracker 或企业 API 集成；
- 需要冻结经过验证的技能集合，避免上游变化直接影响团队。

二次封装应遵循上游 MIT 许可证，并保留版权声明；不要把上游原文直接复制后当作独立原创产品发布。

### 14.4 更新与版本同步策略

本项目适合纳入仓库的双版本跟踪体系：
- **上游版本**：记录 Git Tag / Release、主分支 Commit。
- **封装版本**：若未来产生自有适配层，独立记录适配层版本和支持的上游版本。
- **变更影响**：检查技能新增、删除、重命名、调用类型变化、路由变化、外部工具要求变化、Manifest 变化和破坏性行为调整。
- **更新策略**：自动检测与生成差异报告；更新前阅读相关 Skill 文件并运行代表性场景；人工确认后再发布。
- **回滚**：保留上一个已验证版本的文件或插件版本，不直接追随未经验证的 main。

若直接使用 Claude Code 等托管插件，更新机制由宿主 Marketplace 控制；若使用 Skills CLI 复制可编辑文件，则由使用者主动运行更新命令。两者的更新延迟和控制权不同。

## 15. 常见问题与排错

### 安装后 Agent 不认识技能

- 确认安装目标是当前 Agent 实际读取的目录。
- 检查宿主是否支持 Agent Skills 或对应插件格式。
- 重启 / 重新加载宿主后再检查技能列表。
- 避免同时安装插件包和 Skills CLI 复制版本造成重复。

### `/setup-matt-pocock-skills` 运行后没有自动写配置

这是预期的交互设计：它要求先探索、展示结果、让用户确认，再写文件。检查 Agent 是否完整执行了确认阶段。

### Skill 试图调用不存在的工具

检查宿主是否有 Skill 工具、子 Agent、后台执行、终端、Git Worktree 或对应 CLI。Skill 文档不能凭空提供宿主缺失的能力。

### Issue / PR 操作失败

检查 `docs/agents/issue-tracker.md`、Git Remote、`gh auth status` 或 `glab auth status`，并确认当前账户对目标仓库有相应权限。

### 自动调用与手动调用行为不同

检查 Skill frontmatter 的调用配置，以及宿主对隐式调用的实现。用户主动调用和模型自动调用是不同的触发路径。

### 更新后出现重复或旧版本

先确认采用的是插件安装还是文件复制路线；检查插件市场是否启用自动更新、Skills CLI 是否执行了 update，以及是否同时存在两套安装。

## 16. 综合评估

| 维度 | 评价 | 说明 |
|---|---|---|
| 实际使用价值 | 高 | 将常见工程实践整理成可重复调用的 Agent 流程 |
| 独特性 | 中高 | 单项方法并非全新，价值主要在组合、路由和细节约束 |
| 成熟度 | 中高 | 有稳定插件清单、版本、变更日志和多个宿主安装路线 |
| 文档质量 | 高 | README、技能文件、参考资料和版本变更记录较完整 |
| 可组合性 | 高 | 技能职责相对清晰，可按需安装和组合 |
| 宿主兼容性 | 中高 | 覆盖多个编码 Agent，但每个宿主的工具能力不同 |
| 易用性 | 中 | 安装不复杂；要真正用好需要理解流程和配置仓库 |
| 安全可控性 | 中 | 强调用户参与和确认，但执行权限取决于宿主 |
| 维护活跃度 | 高（核验时） | 主分支近期更新，存在正式 Release 和变更日志 |
| 长期参考价值 | 高 | 对 Agent 工作流设计、Skill 文档结构和上下文管理有参考意义 |

### 主要优势

1. 技能粒度清楚，用户主动流程与模型主动流程有区分。
2. 不只描述“做什么”，还明确阶段关系、前置条件、产物和部分失败边界。
3. 将任务图、上下文交接、领域文档和反馈闭环纳入工程流程。
4. 对不同宿主提供多条分发路径，并将 Beta 内容与稳定插件分开。
5. MIT 许可证，便于研究、修改和在遵守声明条件下再分发。

### 主要局限

1. 很多流程依赖宿主工具能力，跨 Agent 的实际行为不可能完全一致。
2. 体系面向软件工程，普通问答或轻量任务使用价值有限。
3. 部分流程较重；小需求若机械地走完整套 Spec / Ticket / Review，可能增加成本。
4. Skill 是提示与流程规范，不是强制执行引擎；Agent 可能遗漏步骤，必须用真实产物和测试结果验收。
5. 上游更新较快，路由器、技能目录和调用机制需要持续同步。

## 17. 最终收录结论与建议实践路线

**最终结论：推荐收录（recommended）。**

收录理由：
- 有清晰的实际用途，不只是提示词合集；
- 技能之间形成完整的工程工作流；
- 对 Agent Skills 的模块化、触发机制、渐进披露和跨会话状态管理具有较高参考价值；
- 维护和发布机制可追踪；
- 可直接安装使用，不必先做二次封装。

建议实践顺序：

1. 先阅读 `ask-matt`，理解整套技能的路由关系。
2. 选择一个非关键测试仓库，安装少量核心技能。
3. 运行 `setup-matt-pocock-skills`，先审阅配置草案。
4. 用一个真实的小功能体验 grill → spec → tickets → implement → TDD → review。
5. 再测试 `handoff`，观察新会话是否能仅凭交接文档接续。
6. 最后再评估 `implement-spec`、`wayfinder` 等需要更强宿主能力的流程。

**不建议一开始就全量安装并强制所有项目采用该工作流。** 将其视为可选工具箱，根据任务规模主动调用更合适。

---

## 18. 证据边界

- **上游明确说明**：项目定位、安装方式、技能用途、调用分类、Beta 内容不进入插件、初始化流程、许可证。
- **本次仓库核验**：主分支目录、插件 Manifest、Marketplace 文件、package 版本、最新 Release、README、技能清单和 MIT LICENSE。
- **工程分析**：宿主能力依赖、并行实现要求、权限风险、二次封装建议和选型比较。
- **尚未实测**：本机各 Agent 的安装成功率、不同宿主的自动更新时延、并行子 Agent 的实际质量和跨平台一致性。
- **动态信息**：Stars、Forks、最新 Commit、Release、技能清单和宿主命令均可能变化；使用前应回到上游核验。
