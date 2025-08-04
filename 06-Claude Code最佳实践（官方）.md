---
banner: 
date_created: Friday, July 11th 2025, 12:00:07 am
date_updated: Tuesday, July 22nd 2025, 12:36:30 am
title: 06-Claude Code最佳实践（官方）
author: hacket
categories:
  - AI
category: Vibe Coding
tags: [AI, ClaudeCode]
toc: true
description: 
dg-publish: true
dg-enable-search: true
dg-show-local-graph: true
dg-show-toc: true
dg-show-file-tree: true
image-auto-upload: true
feed: show
format: list
aliases: [Claude Code 最佳实践]
linter-yaml-title-alias: Claude Code 最佳实践
---

# Claude Code 最佳实践

## 一、Customize your setup 定制你的个性化开发环境

Claude Code 通过自动抓取 `context into prompts`，这一过程会消耗时间和 Token。通过精细化配置环境，你可以显著优化其性能和准确性。

### CLAUDE.md files

`CLAUDE.md` 是一个特殊文件，Claude Code 在启动对话时会自动加载其内容作为上下文。这使其成为记录项目关键信息的理想场所，内容可包括：

- **常用命令**：例如 `npm run build`、`npm run typecheck`。
- **核心文件与函数**：项目中的关键模块或工具函数。
- **代码风格指南**：如使用 ES 模块、解构导入等。
- **测试说明**：如何运行单元测试或端到端测试。
- **仓库规范**：分支命名规则、合并策略等。
- **开发环境设置**：例如 `pyenv` 的使用、特定编译器的要求。
- **项目特有的行为**：任何需要注意的警告或非预期行为。
- 其他你想让 Claude 记住的信息

`CLAUDE.md` 没有固定格式，但建议保持**简洁易读**。你可以将它放置在多个位置，Claude 会智能地加载它们：

```markdown
# Bash commands
- npm run build: Build the project
- npm run typecheck: Run the typechecker

# Code style
- Use ES modules (import/export) syntax, not CommonJS (require)
- Destructure imports when possible (eg. import { foo } from 'bar')

# Workflow
- Be sure to typecheck when you’re done making a series of code changes
- Prefer running single tests, and not the whole test suite, for performance
```

- **项目根目录**：最常见的用法，可将其命名为 `CLAUDE.md` 并提交至 Git，与团队共享。若为个人配置，可命名为 `CLAUDE.local.md` 并加入 `.gitignore`。
- **父目录**：适用于 Monorepo 结构，Claude 会自动加载所有父目录中的 `CLAUDE.md` 文件。
- **子目录**：当你处理子目录中的文件时，Claude 会按需加载对应子目录的 `CLAUDE.md`。
- **用户主目录**：`~/.claude/CLAUDE.md` 中的配置将应用于所有 Claude 会话。

你可以通过运行 `/init` 命令，让 Claude 自动为你生成一个基础的 `CLAUDE.md` 文件。

### 调优 CLAUDE.md

你的所有 `CLAUDE.md` 会成为 Claude 的 prompts，需要需要精炼高频的作为 prompt。一个普遍的错误是添加了大量内容而不能重复使用。花时间进行实验并确定什么可以产生最佳的 mode instruction。

你可以手动添加内容到 `CLAUDE.md`，或按 `#` 添加内容到 `CLAUDE.md` 中去。很多工程师在 coding 的时候高频的使用 `#` 去记录 `commands`，`files` 和 `style guidelines`，然后将 `CLAUDE.md` 添加到版本控制中去。

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250716000424814.png)

在 `Anthropic`，他们偶尔会通过 [prompt improver](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/prompt-improver) 跑 `CLAUDE.md`，经常调整指令（如添加强调指令 `IMPORTANT` 或 `YOU MUST`）来提升 Claude Code 遵守规则

### 管理 Claude 的工具许可名单

为确保安全，Claude Code 默认会对所有可能修改系统的操作（如文件写入、执行 shell 命令、MCP Tools 等）请求用户许可。你可以通过自定义**许可名单（allowlist）** 来提升效率，允许其自动执行你信任的安全操作。

管理许可名单有四种方式：

- **会话中授权**：当 Claude 请求权限时，选择 "**Always allow**"。
- **使用 `/permissions` 命令**：在会话中添加或移除工具权限，例如 `Edit` 允许文件编辑，`Bash(git commit:*)` 允许 Git 提交，`mcp__puppeteer__puppeteer_navigate` 允许使用 Puppeteer MCP 服务器导航。
- **手动编辑配置文件**：修改项目下的 `.claude/settings.json` 或全局的 `~/.claude.json`。（前者建议纳入到版本控制中去）
- **使用命令行标志**：通过 `-allowedTools` 为特定会话指定权限。

### 安装 GitHub CLI

如果你的工作流涉及 GitHub，强烈建议安装 `gh` 命令行工具。Claude 能够利用它来创建 issue、开启 PR、读取评论等。若未安装，Claude 仍可回退使用 GitHub API 或 MCP 服务器。

## 二、为 Claude 提供更多工具

Claude 可以利用你的 Shell 环境和更复杂的外部工具 (通过 MCP 和 RESET APIs)，从而扩展其能力边界。

### 将 Claude 与 bash 工具结合使用

Claude Code 会继承你的 Bash 环境，使其能够访问所有已安装的工具。Claude Code 知道通用的 unix 工具和 gh，对于自定义的脚本或不常见的工具，你需要通过以下方式告知 Claude 如何使用：

- 提供工具名称和用法示例。
- 让 Claude 运行 `-help` 命令来学习工具的文档。
- 将常用工具的说明记录在 `CLAUDE.md` 中。

### Claude 使用 MCP

Claude Code 同时是 **MCP（Model-Context Protocol）** 的服务器和客户端，可以连接到任意数量的 MCP 服务器以获取其工具。这使得 Claude 能够与 Puppeteer（浏览器自动化）、Sentry（错误监控）等外部服务交互。你可以通过项目配置、全局配置或 `.mcp.json` 文件来管理这些连接。

作为 client，可以通过 3 种方式连接任意数量的 MCP servers 来访问他们的工具：

- 项目配置 Project config（Claude Code 这个目录运行时可用）
- 全局配置（对所有项目可用）
- `.mcp/json` 文件（针对任何工作在这个 codebase 上的人可用）

使用 `MCP` 时，使用 `--mcp-debug` 标志启动 Claude 也有助于识别配置问题。

### 创建自定义斜杠命令

对于重复性工作流（如 debugging loops、log analysis 等），你可以在 `.claude/commands` 目录下创建 Markdown 文件作为**自定义斜杠命令**。这些文件可以包含提示词模板，并通过 `$ARGUMENTS` 关键字接收参数。

例如，这里有个你能自动 pull 和 fix Github isseu 的命令，创建一个名为 `.claude/commands/fix-github-issue.md` 的文件：

```shell
Please analyze and fix the GitHub issue: $ARGUMENTS.

Follow these steps:

1. Use `gh issue view` to get the issue details
2. Understand the problem described in the issue
3. Search the codebase for relevant files
4. Implement the necessary changes to fix the issue
5. Write and run tests to verify the fix
6. Ensure code passes linting and type checking
7. Create a descriptive commit message
8. Push and create a PR

Remember to use the GitHub CLI (`gh`) for all GitHub-related tasks.
```

保存后，你就可以在 Claude Code 中使用 `/project:fix-github-issue 1234` 命令，让 Claude 自动修复指定的 GitHub issue。

也可以保存到个人目录：`~/.claude/commands`

## 三、探索常见的高效工作流

Claude Code 的灵活性允许你自由设计工作流。以下是社区中沉淀下来的一些高效模式。

### Explore, plan, code, commit

这是一个适用于多种复杂任务的通用工作流，它强调在编码前进行充分的思考和规划。

#### 1、explore

要求 Claude 阅读相关文件、图片或 URL。

- 提供一般的指示：`read the file that handles logging`
- 或指定的 filename：`read logging.py`
- 但明确指示它**暂时不要编写任何代码**

这是工作流中的一部分，考虑是要用 subagent，特别是针对复杂的问题。告诉 Claude 使用 subagent 来验证细节或调查可能遇到的特定问题（尤其是在对话或任务的早期），往往会保留上下文的可用性，而不会在效率损失方面造成太大的负面影响。

#### 2、plan

让 Claude 制定解决问题的计划。使用 "**think**"、"**think hard**" 或 "**ultrathink**" 等关键词让 Claude 触发延伸的 thinking mode，可以给予 Claude 更多的计算时间来评估不同方案。

这些特定的表述与系统中 thinking budget 的逐步提升程度直接对应着：

"think" < "think hard" < "think harder" < "ultrathink."

每个级别都会逐渐为 Claude 分配更多的 thinking budget。

如果此步骤的结果看起来合理，您可以让 Claude 创建一个文档或 GitHub 问题来说明其计划，以便当实施（步骤 3：code）不符合您的要求时您可以重置到此位置。

#### 3、code

在确认计划后，让 Claude 开始实施编码。这也是一个很好的地方，可以要求它在实施解决方案的各个部分时明确验证其解决方案的合理性。

#### 4、commit

让 Claude 提交代码、创建 PR，并更新相关文档。

步骤 1-2 是最重要的，没有它们 Claude 倾向于直接跳转到 code 阶段。当然有时候这是你想要的，首先让 Claude 去 resourch 和 plan 可以显著的提升需要深入思考的问题的表现。

### Write tests, commit; code, iterate, commit 测试驱动开发（TDD）

对于可通过 `unit`、`integration` 或 `end-to-end tests` 轻松验证的更改，这是 Anthropic 最喜欢的工作流。测试驱动开发（TDD）通过 agentic coding 变得更加强大：

- **编写 tests 在期望的 input/output**：让 Claude 根据预期输入输出编写测试用例。
明确说明您正在进行 `test-driven development`，以避免创建模拟实现，即使对于代码库中尚不存在的功能也是如此。
- **告诉 Claude 运行 tests 并确认失败**： 运行测试，确保它们因功能未实现而失败。明确告诉它不要在此阶段编写任何实现代码通常会有所帮助。
- **提交测试**：如果你满意话，将测试用例提交到版本控制。
- **实现功能并通过测试**：指示其不要修改测试。告诉 Claude 继续进行，直到所有测试通过。Claude 通常需要多次迭代来编写代码、运行测试、调整代码，然后再次运行测试。
- **提交代码**：在所有测试通过后，提交最终实现。

### Write code, screenshot result, iterate 视觉驱动开发

与 TDD 类似，你可以为 Claude 提供视觉目标，尤其适用于 UI 开发。

1. **提供截图工具**：通过 Puppeteer MCP 服务器或 iOS 模拟器 MCP 服务器，让 Claude 能够截取浏览器或应用的界面。
2. **提供视觉模拟**：通过复制粘贴、拖拽或文件路径的方式，将图片提供给 Claude。
3. **迭代实现**：要求 Claude 编写代码、截图、比对视觉稿，并循环迭代直至结果匹配。
4. **提交**：满意后提交代码。

与人类一样，Claude 的输出往往会随着迭代而显著提升。虽然第一个版本可能不错，但经过 2-3 次迭代后，效果通常会更好。请为 Claude 提供工具来查看其输出，以获得最佳效果。

### Safe YOLO mode

对于修复 lint 错误或生成样板代码等低风险任务，你可以使用 `claude --dangerously-skip-permissions` 标志让 Claude 在无人监督的情况下连续工作。像 fix lint errors 或生成样板代码。

**警告**：此模式存在数据丢失、系统损坏甚至数据泄露的风险。为将风险降至最低，请务必在**没有网络访问的隔离容器**（如 [Docker Dev Container](https://github.com/anthropics/claude-code/tree/main/.devcontainer)）中使用此模式。

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250717001821774.png)

### Codebase Q&A

当加入新的 codebase 时，使用 Claude Code 进行学习和探索。你可以向 Claude 提出你在结对编程时会问项目中其他工程师的那些问题。Claude 可以自动搜索 codebase 来回答一些常见的问题，例如：

- How does logging work?
- How do I make a new API endpoint?
- What does `async move { … }` do on line 134 of `foo.rs`?
- What edge cases does `CustomerOnboardingFlowImpl` handle?
- Why are we calling `foo()` instead of `bar()` on line 333?
- What's the equivalent of line 334 of `baz.py` in Java?
在 Anthropic，以这种方式使用 Claude Code 已成为我们的核心入职工作流程，显著缩短了上手时间，并减轻了其他工程师的负担。无需任何特殊 prompt！只需提出问题，Claude 就会探索代码来寻找答案。

### 用 Claude 与 Git 和 Github 交互

#### Use Claude to interact with git

许多 Anthropic 工程师超过 90% 的 Git 和 GitHub 操作都交由 Claude 完成，包括：

- **搜索 Git 历史**：回答 " 这个功能是谁负责的？" 或 " 这个 API 为何这样设计？" 等问题。
- **编写提交信息**：Claude 会根据代码变更和近期历史自动生成高质量的 commit message。
- **处理复杂 Git 操作**：如文件回滚、解决 rebase 冲突等。

#### Use Claude to interact with GitHub

Claude Code 可以管理许多 GitHub 交互：

- **Creating pull requests** Claude 理解简写 "`pr`"，并将根据差异和周围环境生成适当的提交消息。
- **Implementing one-shot resolutions** 对于简单的 code review comments：只需告诉它修复您的 PR 上的 comments（可选，给它更具体的说明）并在完成后推回到 PR 分支。
- **Fixing failing builds** 修复构建失败或 lint 警告
- **Categorizing and triaging open issues** 通过要求 Claude 循环浏览 GitHub 上的未解决 issues，对未解决 issues 进行分类和分类

这样就无需在自动执行日常任务时记住 `gh` 命令行语法。

### Use Claude to work with Jupyter notebooks

Anthropic 的研究人员和数据科学家使用 Claude Code 读取和写入 Jupyter notebooks。Claude 可以解释输出结果（包括图像），从而提供一种快速探索和交互数据的方法。它无需任何提示或工作流程，但我们建议的工作流程是在 VS Code 中同时打开 Claude Code 和 .ipynb 文件。

您还可以要求 Claude 在向同事展示您的 Jupyter notebook 之前对其进行清理或美观改进。

具体告诉它让 notebook 或其数据可视化 " 美观 " 往往有助于提醒它正在针对人类的观看体验进行优化。

## 四、Optimize your workflow 优化你的工作流

以下技巧适用于所有工作流，能进一步提升你与 Claude 协作的效率。

### 指令要具体明确

指令越具体，Claude 的首次成功率就越高。明确的指令能减少后期反复修正的成本。

For example:

| Poor                                             | **Good**                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| add tests for foo.py                             | write a new test case for foo.py, covering the edge case where the user is logged out. avoid mocks                                                                                                                                                                                                                                                                                                                                                      |
| why does ExecutionFactory have such a weird api? | look through ExecutionFactory's git history and summarize how its api came to be                                                                                                                                                                                                                                                                                                                                                                        |
| add a calendar widget                            | look at how existing widgets are implemented on the home page to understand the patterns and specifically how code and interfaces are separated out. HotDogWidget.php is a good example to start with. then, follow the pattern to implement a new calendar widget that lets the user select a month and paginate forwards/backwards to pick a year. Build from scratch without libraries other than the ones already used in the rest of the codebase. |

Claude 可以推断意图，但无法读懂他人心思。具体化才能更好地与预期保持一致。

### Give Claude images

Claude 通过多种方法擅长处理图像和图表：

- **粘贴截图**：直接将截图粘贴到输入框（Mac：cmd+ctrl+shift+4）。
- **拖拽图片**：将图片文件拖入终端。
- **提供文件路径**：使用 Tab 自动补全快速引用仓库中的任何文件或目录。
- **提供 URL**：粘贴 URL 让 Claude 读取网页内容，如文档、博客等。
向 Claude 提供图片作为上下文：

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250717003615076.png)

### Mention files you want Claude to look at or work on

使用 Tab 补全功能快速引用存储库中任何位置的文件或文件夹，帮助 Claude 找到或更新正确的资源。

通过 Tab 补全快速引用文件：

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250717004046758.png)

### Give Claude URLs

将特定的 URL 粘贴到 prompt 旁边，以便 Claude 获取和阅读。为了避免对相同域名（例如 docs.foo.com）的权限提示，请使用 /permissions 将域名添加到您的允许列表中。

向 Claude 提供 URL 作为上下文：

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250717004106527.png)

### Course correct early and often

与其让 Claude 完全自主工作 (auto-accept mode: `shift+tab` to toggle)，不如成为一个积极的协作者。及时的引导和修正能产出更好的结果。

以下四种工具有助于纠正航向：

- **让 Claude 制定一个计划** 在编码之前。明确地告诉它，在你确认它的计划看起来不错之前不要编码。
- **按 ESC 打断** 按 `Escape` 键可以随时中断 Claude 的当前操作，然后给出新的指令。
- **回溯** 连按两次 `Escape` 键可以回到历史记录，编辑之前的提示词，从某个节点开始探索新的方向。
- **撤销** 要求 Claude 撤销刚才的修改。通常与选项 2 结合使用以采取不同的方法。
尽管 Claude Code 偶尔会在第一次尝试时完美地解决问题，但使用这些校正工具通常可以更快地产生更好的解决方案。

### 使用 /clear 保持上下文清晰

在长会话中，上下文窗口可能会被不相关的信息填满，影响性能。在切换任务时，频繁使用 `/clear` 命令可以重置上下文，让 Claude 更专注于当前任务。

### 使用清单和草稿处理复杂任务

对于多步骤的复杂任务（如代码迁移、修复大量 lint 错误），可以让 Claude 使用一个 Markdown 文件 (或 GitHub issue) 作为**清单（checklist）和草稿板（scratchpad）**。

例如，修复 lint 错误时，可以指示 Claude：

1. 运行 lint 命令，将所有错误（with filenames and line numbers）输出到 `lint-errors.md` 文件中，并格式化为清单。
2. 逐一处理清单中的每个问题，修复、验证，然后勾选掉该项。

### Pass data into Claude

有几种方法可以向 Claude 提供数据：

- 直接复制并粘贴到你的提示中（最常见的方法）
- 通过管道传输到 Claude 代码（例如 `cat foo.txt | claude`），特别适用于日志、CSV 和大数据
- 告诉 Claude 通过 bash 命令、MCP 工具或自定义斜线命令提取数据
- 要求 Claude 读取文件或获取 URL（也适用于图像）

大多数会话都涉及这些方法的组合。例如，您可以输入日志文件，然后告诉 Claude 使用工具提取其他上下文来调试日志。

## 五、使用无头模式实现自动化

Claude Code 的**无头模式（headless mode）** 专为非交互式环境设计，如 CI/CD、Git 钩子、自动化脚本等。

- 使用 `-p` 标志并附带一个提示词即可启用。
- 使用 `--output-format stream-json` 用于流式 JSON 输出。

请注意，无头模式在会话之间不会持续存在。您必须在每个会话中触发它。

### Use Claude for issue triage 用于 Issue 分类

你可以设置 GitHub Actions，在创建新 issue 时触发无头模式的 Claude，让它分析 issue 内容并自动打上合适的标签。

### Use Claude as a linter

传统 linter 无法检测主观问题。你可以让 Claude 在 CI 流程中进行代码审查，找出拼写错误、过时的注释、误导性的变量名等问题。

## 六、进阶：多 Claude 协同工作流

通过并行运行多个 Claude 实例，可以解锁更强大的工作模式。

### 一个编码，一个验证

一个简单但有效的方法是让一个 Claude 编写代码，另一个 Claude 审查或测试代码。与多个工程师合作类似，有时拥有独立的 context 是有益的：

1. 让一个 Claude 实例编写代码。
2. 在另一个终端中启动第二个 Claude 实例（或在当前终端使用 `/clear`）。
3. 让第二个 Claude 审查第一个实例的工作。
4. 再启动一个 Claude（或再次 `/clear`），让它结合代码和审查反馈进行修改。
5. 让 Claude 根据反馈编辑代码

您可以对测试做类似的事情：让一个 Claude 编写测试，然后让另一个 Claude 编写代码以使测试通过。您甚至可以通过为 Claude 实例提供单独的工作暂存器并告诉它们要写入哪一个以及从哪一个读取来让它们相互通信。

这种上下文分离的模式，类似于人类工程师的结对编程或代码审查，通常能产生比单个 Claude 从头到尾处理所有事情更好的结果。

### 对你的 repo 进行多次 checkout

Anthropic 的许多工程师并没有等待 Claude 完成每个步骤，而是这样做：

- 在单独的文件夹中创建 3-4 个 git checkout
- 在单独的终端选项卡中打开每个文件夹
- 在每个文件夹中启动 Claude 并执行不同的任务
- 循环检查进度并批准/拒绝权限请求

### 使用 Git Worktrees 并行处理任务

这种方法适用于多个独立任务，为多次结账提供了一种更轻量级的替代方案。Git Worktrees 允许您将同一存储库中的多个分支检出到单独的目录中。每个 Git Worktrees 都有自己的工作目录和独立的文件，同时共享相同的 Git 历史记录和 reflog。

使用 git worktrees 可以在项目的不同部分同时运行多个 Claude 会话，每个人都专注于自己独立的任务。例如，你可能让一个 Claude 重构你的身份验证系统，而另一个 Claude 构建一个完全不相关的数据可视化组件。由于任务不重叠，每个 Claude 都可以全速工作，而无需等待其他人的更改或处理合并冲突：

- 创建工作树： `git worktree add ../project-feature-a feature-a`
- 在每个工作树中启动 Claude：`cd ../project-feature-a && claude`
- 根据需要创建其他工作树（在新的终端选项卡中重复步骤 1-2）

**一些提示：**
- 使用一致的命名约定
- 每个工作树维护一个终端选项卡
- 如果您在 Mac 上使用 iTerm2，请设置 Claude 需要关注时的通知
- 对不同的工作树使用单独的 IDE 窗口
- 完成后清理： `git worktree remove ../project-feature-a`

**使用 Git Worktrees 并行处理任务:**
![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250717012407578.png)

### Use headless mode with a custom harness

结合无头模式和自定义脚本，可以实现大规模的自动化工作流。

`claude -p`（无头模式）以编程方式将 Claude Code 集成到更大的工作流程中，同时利用其内置工具和系统提示符。使用无头模式主要有两种模式：

- **Fanning out** 扇出 处理大型迁移或分析（例如，分析数百个日志中的情绪或分析数千个 CSV）：
	- 让 Claude 编写一个脚本来生成任务列表。例如，生成一个包含 2k 个文件的列表，这些文件需要从框架 A 迁移到框架 B。
	- 循环执行每个任务，以编程方式调用 Claude，并赋予其一个任务和一组可用的工具。例如：`claude -p “migrate foo.py from React to Vue. When you are done, you MUST return the string OK if you succeeded, or FAIL if the task failed.” --allowedTools Edit Bash(git commit:*)`
	- 运行脚本几次并优化提示以获得所需的结果。
- **Pipelining** 管道 将 Claude 集成到现有的数据处理管道中。
	- 调用 `claude -p “<your prompt>” --json | your_command`，其中 `your_command` 是处理管道的下一步
	- 就是这样！JSON 输出（可选）可以帮助提供更易于自动化处理的结构。

对于这两种用例，使用 `--verbose` 标志来调试 Claude 调用都会很有帮助。为了获得更清晰的输出，我们通常建议在生产环境中关闭详细模式。

## Ref

- [Claude Code: Best practices for agentic coding](https://www.anthropic.com/engineering/claude-code-best-practices "https://www.anthropic.com/engineering/claude-code-best-practices")
