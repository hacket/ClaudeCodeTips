---
banner: 
date_created: Wednesday, July 23rd 2025, 12:00:22 am
date_updated: Tuesday, August 5th 2025, 12:02:52 am
title: 02-Claude Code基本用法
author: hacket
categories:
  - AI
category: ClaudeCode
tags: [AI, ClaudeCode, VibeCoding]
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
aliases: [Claude Code 基本用法]
linter-yaml-title-alias: Claude Code 基本用法
---

# Claude Code 基本用法

## Claude Code 设置

官方文档：<https://docs.anthropic.com/zh-CN/docs/claude-code/settings#%E8%AE%BE%E7%BD%AE%E6%96%87%E4%BB%B6>

可以使用 `全局` 和 `项目级` 设置以及 `环境变量` 配置 Claude Code。

### 设置文件 settings.json

`settings.json` 文件是我们通过分层设置配置 Claude Code 的官方机制：

- **用户设置** 在 `~/.claude/settings.json` 中定义，适用于所有项目。
- **项目设置** 保存在您的项目目录中：
	- `.claude/settings.json` 用于检入源代码控制并与团队共享的设置
	- `.claude/settings.local.json` 用于个人偏好和实验。不纳入版本控制，Claude Code 会在创建时配置 git 忽略 `.claude/settings.local.json`。
- 对于 Claude Code 的企业部署，我们还支持**企业管理策略设置**。这些设置优先于用户和项目设置。系统管理员可以在 macOS 上将策略部署到 `/Library/Application Support/ClaudeCode/managed-settings.json`，在 Linux 和通过 WSL 的 Windows 上部署到 `/etc/claude-code/managed-settings.json`。

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run lint)",
      "Bash(npm run test:*)",
      "Read(~/.zshrc)"
    ],
    "deny": [
      "Bash(curl:*)"
    ]
  },
  "env": {
    "CLAUDE_CODE_ENABLE_TELEMETRY": "1",
    "OTEL_METRICS_EXPORTER": "otlp"
  }
}
```

**示例：**

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "http://localhost:4141",
    "ANTHROPIC_AUTH_TOKEN": "dummy",
    "ANTHROPIC_MODEL": "claude-sonnet-4",
    "ANTHROPIC_SMALL_FAST_MODEL": "claude-3.7-sonnet",
    "DISABLE_NON_ESSENTIAL_MODEL_CALLS": "1",
    "DISABLE_TELEMETRY": "1",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": "1"
  }
}
```

#### 可用设置

`settings.json` 支持多个选项：

| 键                     | 描述                                                                                                                | 示例                              |
| --------------------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------- |
| `apiKeyHelper`        | 自定义脚本，在 `/bin/sh` 中执行，用于生成认证值。此值通常作为模型请求的 `X-Api-Key`、`Authorization: Bearer` 和 `Proxy-Authorization: Bearer` 头发送 | `/bin/generate_temp_api_key.sh` |
| `cleanupPeriodDays`   | 本地保留聊天记录的时间长度（默认：30 天）                                                                                            | `20`                            |
| `env`                 | 将应用于每个会话的环境变量                                                                                                     | `{"FOO": "bar"}`                |
| `includeCoAuthoredBy` | 是否在 git 提交和拉取请求中包含 `co-authored-by Claude` 署名（默认：`true`）                                                          | `false`                         |
| `permissions`         | 权限结构见下表。                                                                                                          |                                 |

#### permissions 设置

| 键                              | 描述                                                                                                                                                | 示例                               |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------- |
| `allow`                        | 允许工具使用的 [权限规则](https://docs.anthropic.com/zh-CN/docs/claude-code/iam#configuring-permissions) 数组                                                    | `[ "Bash(git diff:*)" ]`         |
| `deny`                         | 拒绝工具使用的 [权限规则](https://docs.anthropic.com/zh-CN/docs/claude-code/iam#configuring-permissions) 数组                                                    | `[ "WebFetch", "Bash(curl:*)" ]` |
| `additionalDirectories`        | Claude 可以访问的额外 [工作目录](https://docs.anthropic.com/zh-CN/docs/claude-code/iam#working-directories)                                                   | `[ "../docs/" ]`                 |
| `defaultMode`                  | 打开 Claude Code 时的默认 [权限模式](https://docs.anthropic.com/zh-CN/docs/claude-code/iam#permission-modes)                                                 | `"allowEdits"`                   |
| `disableBypassPermissionsMode` | 设置为 `"disable"` 以防止激活 `bypassPermissions` 模式。参见 [管理策略设置](https://docs.anthropic.com/zh-CN/docs/claude-code/iam#enterprise-managed-policy-settings) | `"disable"`                      |

#### 设置的优先级

设置按优先级顺序应用：

1. 企业策略（参见 [IAM 文档](https://docs.anthropic.com/zh-CN/docs/claude-code/iam#enterprise-managed-policy-settings)）
2. 命令行参数
3. 本地项目设置
4. 共享项目设置
5. 用户设置

### 环境变量

Claude Code 支持以下环境变量来控制其行为：

> 所有环境变量也可以在 [`settings.json`](https://docs.anthropic.com/zh-CN/docs/claude-code/settings#available-settings) 中配置。这作为为每个会话自动设置环境变量的方式很有用，或者为整个团队或组织推出一组环境变量。

见：[Claude Code 设置 - Anthropic](https://docs.anthropic.com/zh-CN/docs/claude-code/settings#%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F)

## 初始化

运行 `/init` 命令让 Claude Code 扫描你的代码库，分析项目结构、依赖和技术栈，生成一个 `CLAUDE.MD` 文件。这个文件会记录项目的关键信息，作为 Claude Code 后续工作的基础，相当于给 AI 一个 " 项目蓝图 "。

## Claude Code 模式

### 规划模式 (plan mode)

Claude Code 有一个内置的规划模式，当你按两次 `Shift+Tab` 时触发。在这种模式下，Claude 不会写入你的文件系统。

### Yolo 模式

这对我来说可能是**极其不负责任的**，但我现在主要使用 `claude --dangerously-skip-permissions` 运行 Claude。

**使用场景：**

- 这不是所有事情都必要的
- 但如果我让 Claude 处理一些**长期运行的任务**
- 我**真的不想**每分钟都必须切换焦点回到它，因为它使用新的终端命令

**配置方法：** 我在我的 zsh 配置文件中设置了这个：

```shell
alias yolo="claude --dangerously-skip-permissions"
```

**副作用：** 有趣的是，现在 Claude 可以做任何它想做的事，我也**更频繁地遇到速率限制配额警告**。

## MCP 服务器

官方文档：<https://docs.anthropic.com/zh-CN/docs/claude-code/mcp>

### 配置 MCP 服务器

- 添加 MCP stdio 服务器

```shell
# 基本语法
claude mcp add <name> <command> [args…]

# 示例：添加本地服务器
claude mcp add my-server -e API_KEY=123 -- /path/to/server arg1 arg2
```

- 添加 MCP SSE 服务器

```shell
# 基本语法
claude mcp add --transport sse <name> <url>

# 示例：添加 SSE 服务器
claude mcp add --transport sse sse-server https://example.com/sse-endpoint

# 示例：添加带有自定义标头的 SSE 服务器
claude mcp add --transport sse api-server https://api.example.com/mcp -e X-API-Key=your-key
```

- 添加 MCP HTTP 服务器名称

```shell
# 基本语法
claude mcp add --transport http <name> <url>

# 示例：添加可流式传输的 HTTP 服务器
claude mcp add --transport http http-server https://example.com/mcp

# 示例：添加带有身份验证标头的 HTTP 服务器
claude mcp add --transport http secure-server https://api.example.com/mcp -e Authorization="Bearer your-token"
```

### 管理您的 MCP 服务器

```shell
# 列出所有已配置的服务器
claude mcp list

# 获取特定服务器的详细信息
claude mcp get my-server

# 删除服务器
claude mcp remove my-server

# 使用 /mcp 命令来查看 MCP 服务器相关信息：
```

### 添加 MCP

#### 必要的 MCP

```shell
# 添加context7
claude mcp add --transport http context7 https://mcp.context7.com/mcp

# 添加sequential-thinking
claude mcp add sequential-thinking npx @modelcontextprotocol/server-sequential-thinking

# 添加puppeteer
npx @modelcontextprotocol/server-puppeteer

claude mcp add puppeteer npx @modelcontextprotocol/server-puppeteer

# 添加magic (https://21st.dev/magic/onboarding?step=create-component)
claude mcp add magic npx @21st-dev/magic@latest --env API_KEY=你的api key

```

#### Puppeteer：浏览器自动化操作

<https://github.com/modelcontextprotocol/servers-archived/tree/main/src/puppeteer>

**一般添加方法如下：**

```shell
claude mcp add puppeteer npx -- -y @modelcontextprotocol/server-puppeteer
```

**通过 JSON 方式添加：**

```shell
claude mcp add-json -s user puppeteer '{  
  "command": "npx",  
  "args": ["-y", "@modelcontextprotocol/server-puppeteer"]  
}'
```

> 使用 `-s user` 标志，可以将 MCP 服务器添加到全局配置（可以在 `~/.claude.json` 文件中查看），而不是只针对某个项目，默认不填为 `local`  即当前项目。

**使用：**

> 打开搜索用户页面，按 ID 搜索 6 并返回搜索出来的用户信息

**查看添加**：

```shell
claude mcp add-json -s user context7 '{ "command": "npx", "args": ["-y", "@upstash/context7-mcp"] }'
```

#### context7

<https://context7.com/>

<https://github.com/upstash/context7>

它可以为大模型和 AI 代码编辑器提供最新（或者特定版本）的**文档、库、代码、信息**等，避免使用过时的数据，

在 Claude Code 中进行导入：

```shell
claude mcp add-json -s user context7 '{ "command": "npx", "args": ["-y", "@upstash/context7-mcp"] }'
```

#### Figma

<https://help.figma.com/hc/en-us/articles/32132100833559-Guide-to-the-Dev-Mode-MCP-Server>

## Claude SDK

### Claude SDK 特性

- Claude 有一个**SDK**
- 它**非常强大**，特别是如果你乐意处理 stream-json 输出格式
- 但即使对于小事情，能够直接向 claude 传递提示并让它打印回复也为**创建快捷方式**提供了很好的机会

**实用示例 - 自动 Git 提交：** 例如，我在我的路径中有一个 gcauto 可执行文件，它执行以下操作：

```shell
#!/bin/bash
git commit -m "$(claude -p "查看暂存的git更改并创建总结性的git提交标题。只回应标题，不要确认。")"
```

**使用方法：** 所以每当我现在提交东西时，我只是**暂存它们**并运行 gcauto。

### 使用 SDK 自动化操作

以**自动生成 Git 提交标题**为例：

```shell
npm install @anthropic-ai/claude-sdk
```

创建脚本 `git-commit-title.js`：

```js
import { ClaudeSDK } from '@anthropic-ai/claude-sdk';
import { execSync } from 'child_process';

const sdk = new ClaudeSDK();

async function generateCommitTitle() {
  const diff = execSync('git diff --staged').toString();
  if (!diff.trim()) return console.log("没有暂存的更改。");

  const prompt = `
  Based on the following git diff, create a concise, one-line git commit title in Conventional Commit format.
  Only reply with the title itself.

  Diff:
  ---
  ${diff}
  `;
  const response = await sdk.sendMessage(prompt);
  console.log(response.message.trim());
}

generateCommitTitle();
```

然后配合：

```shell
git commit -m "$(node git-commit-title.js)"
```

自动生成符合规范的提交信息。

**注意：** Claude SDK 是为了让你「在不打开 Claude 窗口、自动化批量操作、或者集成到 CI 流程」中使用 Claude 的智能。

**比如：**  
- 自动生成 Git 提交标题（如你提到的例子）  
- 批量分析文件夹中所有文件的结构  
- 每天自动读取数据库数据，交给 Claude 总结写日报  
- 集成到 CI/CD：测试失败时调用 Claude 自动解释错误原因  
- 定时清理日志，生成摘要并归档发送给管理员

## 让 Claude RTFM（阅读文档）

**新手错误：** 我在开始时犯的一个**新手错误**是告诉 Claude 用**框架 x**建立一个新项目。
**问题分析：**

- 当然，这是可行的
- 但我注意到 Claude 经常使用**过时的设置和项目结构**
- 特别是对于**快速发展的前端框架**

**解决方案：** 我现在做的是要求 Claude**先阅读手册**。这可以通过不同的方式完成：

| 方法   | 描述                          |
| ---- | --------------------------- |
| 文档链接 | 粘贴文档链接并要求 Claude 先阅读它         |
| 技术调研 | 要求 Claude 找出最先进的技术            |
| 深入研究 | 使用 Task 工具并让 Claude 对特定主题进行深入研究 |

深入研究任务的例子可能看起来像这样：

```shell
> 创建一个深入研究任务，找出如何将网站从tailwind 3迁移到4，然后向我展示结果的简短摘要

⏺ 我将创建一个研究任务来调查从Tailwind CSS v3到v4的迁移，重点关注你的Tailwind UI代码库的具体上下文。

⏺ Task(Tailwind v3到v4迁移研究)
  ⎿  完成 (4个工具使用 · 29.9k tokens · 5分47.6秒)

⏺ 摘要

  从Tailwind CSS v3迁移到v4涉及重大变化，但带来了主要的性能改进（3.5倍更快的构建）和更好的开发者体验。

  要点：
  - 使用自动升级工具：npx @tailwindcss/upgrade@next
  - 配置从JavaScript移动到使用@theme指令的CSS优先
  - 破坏性更改包括放弃的浏览器支持、新的导入语法和移除的弃用实用程序
  - 性能提升：构建现在以微秒计算
  - 容器查询现在是内置的（不需要插件）

  使用升级工具迁移很直接，但计划测试时间以确保所有组件正确渲染。
```

## 向 Claude 粘贴图像

可以向 Claude Code 粘贴图像。只需直接复制图像或将文件拖到你的终端中，它就会作为附件添加到你的下一条消息中。当我想进行小的视觉更改时，我有时会使用这个功能。

## 将 Claude 当作用作类 Unix 程序

Claude 不只是一个交互式 AI，它还可以像一个普通的 Unix 命令行工具那样，融入你的开发流程中。

可以用管道 `|` 为 Claude 输入内容：

```shell
cat build-error.txt | claude -p 'concisely explain the root cause of this build error' > output.txt
```

除了纯文本外，在输出的时候还可以指定输出格式：

**文本格式（默认）:**

```shell
cat data.txt | claude -p 'summarize this data' --output-format text > summary.txt
```

- 仅输出纯文本内容。
- 适合日常对话、摘要、说明等任务。

**JSON 格式：**

```shell
cat code.py | claude -p 'analyze this code for bugs' --output-format json > analysis.json
```

- 输出为 JSON 数组，包含 Claude 的响应、元数据（如 token 成本、运行时间等）。
- 适合将结果嵌入自动化流程或前端展示。

**流式 JSON（stream-json）**

```shell
cat log.txt | claude -p 'parse this log file for errors' --output-format stream-json
```

- 实时逐条输出 JSON 消息，每条是一个独立 JSON 对象。
- 更适合处理大文件、长输出或实时监控等场景。
- 注意：整个输出不是完整 JSON 数组，需逐条处理。

**你甚至还可以把 Claude 加入构建脚本，作为代码审查工具运行**

```shell
{
  "scripts": {
    "lint:claude": "claude -p 'you are a linter. please look at the changes vs. main and report any issues related to typos. report the filename and line number on one line, and a description of the issue on the second line. do not return any other text.'"
  }
}
```

## Git 自动化操作

需要提前安装 `gh` CLI，Claude 会自动执行 PR 操作。

自动创建 PR 示例：

```shell
“帮我把当前分支改动创建一个 Pull Request 到 main 分支，标题为：Feature: Add Registration Validation，并总结改动。”
```

## 测试与调试

Claude 能执行测试 + 修复代码，极大提高效率。

**示例：**

```shell
“运行 tests/auth.test.js 中的测试。如果有失败的测试，分析报错并修复 src/utils/auth.js 中的代码。”
```

## Claude Code memory

Claude Code 可以跨会话记住您的偏好设置，比如样式指南和工作流程中的常用命令。

### 内存类型

Claude Code 提供三种内存位置，每种都有不同的用途：

| 内存类型         | 位置                    | 用途          | 使用案例示例                      |
| ------------ | --------------------- | ----------- | --------------------------- |
| **项目内存**     | `./CLAUDE.md`         | 项目的团队共享指令   | 项目架构、编码标准、常见工作流程            |
| **用户内存**     | `~/.claude/CLAUDE.md` | 所有项目的个人偏好设置 | 代码样式偏好、个人工具快捷方式             |
| **项目内存（本地）** | `./CLAUDE.local.md`   | 个人项目特定偏好设置  | _（已弃用，见下文）_ 您的沙盒 URL、首选测试数据 |

所有内存文件在启动 Claude Code 时都会自动加载到上下文中。

### CLAUDE.md 导入

CLAUDE.md 文件可以使用 `@path/to/import` 语法导入其他文件。以下示例导入了 3 个文件：

```markdown
查看 @README 了解项目概述，查看 @package.json 了解此项目可用的 npm 命令。

# 附加说明
- git 工作流程 @docs/git-instructions.md
```

相对路径和绝对路径都是允许的。

导入用户主目录中的文件是让团队成员提供不会检入存储库的个人指令的便捷方式。以前 `CLAUDE.local.md` 有类似的用途，但现在已弃用，改用导入功能，因为它们在多个 git 工作树中工作得更好。

```markdown
# 个人偏好设置
- @~/.claude/my-project-instructions.md
```

为了避免潜在的冲突，导入不会在 markdown 代码段和代码块内进行评估。

```markdown
此代码段不会被视为导入：`@anthropic-ai/claude-code`
```

导入的文件可以递归导入其他文件，最大深度为 5 跳。您可以通过运行 `/memory` 命令查看加载了哪些内存文件。

### Claude 如何查找内存

Claude Code 递归读取内存：从当前工作目录开始，Claude Code 向上递归到（但不包括）根目录 _/_ 并读取它找到的任何 `CLAUDE.md` 或 `CLAUDE.local.md` 文件。这在大型存储库中工作时特别方便，您在 _foo/bar/_ 中运行 Claude Code，并且在 _foo/CLAUDE.md_ 和 _foo/bar/CLAUDE.md_ 中都有内存。

Claude 还会发现嵌套在当前工作目录下子树中的 CLAUDE.md。它们不会在启动时加载，只有当 Claude 读取这些子树中的文件时才会包含。

### 使用 # 快捷方式快速添加内存

添加内存的最快方法是在输入开头使用 `#` 字符：

```shell
# 始终使用描述性变量名
```

系统会提示您选择要将此内容存储在哪个内存文件中。

### 使用 /memory 直接编辑内存

在会话期间使用 `/memory` 斜杠命令在系统编辑器中打开任何内存文件，以进行更广泛的添加或组织。

### 设置项目内存

假设您想要设置一个 `CLAUDE.md` 文件来存储重要的项目信息、约定和常用命令。

使用以下命令为您的代码库引导一个 CLAUDE.md：

```shell
/init
```

> - 包含常用命令（构建、测试、代码检查）以避免重复搜索
> - 记录代码样式偏好和命名约定
> - 添加项目特定的重要架构模式
> - CLAUDE.md 内存可用于与团队共享的指令和您的个人偏好设置。

### 内存最佳实践

- **要具体**：" 使用 2 空格缩进 " 比 " 正确格式化代码 " 更好。
- **使用结构来组织**：将每个单独的内存格式化为项目符号，并在描述性 markdown 标题下对相关内存进行分组。
- **定期审查**：随着项目的发展更新内存，以确保 Claude 始终使用最新的信息和上下文。

## 提示词编写技巧（Prompt Engineering）

### 推荐写法

明确目标 + 风格 + 限定范围

```
“给我一个现代化的设计”
“创建一个类似 Linear 的仪表盘风格 UI，深色主题，卡片布局”
```

### 避免提示歧义

使用：

- `only modify this function`
- `do not do anything else`
来限制 Claude 的行为范围。

### 两步策略

1. 先说：分析思路并列出重构步骤（先不写代码）
2. 然后：等我确认后再执行代码修改

### 长任务管理技巧

任务太多，难以一次说清楚且容易忘记之前 Claude 的上下文，这时我们就可以用一个 Markdown 文件当作 Claude 的工作笔记本，比如对于跨天任务或大型项目，创建计划文件：`refactor_plan.md`

## 使用之前、恢复历史和回滚功能

#### 使用之前的消息

连续按两次 `ESC` 键可以跳到之前的消息：

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250730233504619.png)

上下方向键选择一条消息，然后就会回到对应的提示词命令行窗口，也可以在此基础重新编辑提示词。

### 恢复历史会话

#### 非交互模式

Claude Code 提供两个选项来恢复之前的对话：

- `claude --continue` 或者 `claude -c`：自动继续最近的对话，无需任何提示。
- `claude --resume` 或者 `claude -r`：显示历史对话选择器；
这两个带参数的命令需要在「**非交互模式**」下进行，也就是还没有进入 Claude Code。

如果需要开启 yolo 模式，可直接在后面加上：`--dangerously-skip-permissions`

#### 交互模式

如果你已经进入了 Claude Code 会话，想恢复到之前的哪个历史会话，可以使用 `/resume` 命令恢复历史会话：

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250730233504626.png)

上下方向键选中一条记录可以恢复会话。

### 导出

`/export` 导出整个对话，方便在不同 IDE（如 Cursor 或 WebStorm）间切换，保持上下文一致。

### 回滚代码

直接发送「**回滚**」即可：

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250730233504627.png)

这个类似 Cursor 的 **checkpoint** 检查点功能，如果不想回滚了，再发送一次「**撤销**」即可：

建议再配合 Git 版本控制管理，以防代码丢失。

## Claude Code 命令

见：[[03-Claude Code命令]]

## Claude Code 技巧

见：[[07-Claude Code使用技巧]]

## Token 成本管理技巧

### 查看成本

#### /cost

```shell
/cost
```

#### ccusage

官方查看消耗，但过于笼统，不够直观，推荐使用 `ccusage` 工具来查看。

```shell
npm install -g ccusage
```

如果要查看自某天开始的消耗：

```shell
ccusage -s 20250721
```

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250730233504628.png)

如果要实时查看消耗：

```shell
ccusage blocks --live
```

Claude Pro / Max 订阅用户可以不用理会消耗，它是按月计费的，不是按使用量计费的。

ccusage 其他常用指令：

```shell
# 基础用法  
ccusage          # 显示每日报告（默认）  
ccusage daily    # 每日 token 使用量及费用  
ccusage monthly  # 月度汇总报告  
ccusage session  # 按会话统计用量  
ccusage blocks   # 5小时计费窗口数据  

# 实时监控  
ccusage blocks --live  # 实时用量仪表盘  

# 筛选与选项  
ccusage daily --since 20250525 --until 20250530  # 指定日期范围  
ccusage daily --json      # 输出 JSON 格式  
ccusage daily --breakdown # 按模型细分费用
```

### 减少上下文消耗

#### clear

重置上下文

#### /compat

```shell
/compact
```

它会清除对话历史记录，但保留上下文中的摘要。这样做的好处是：

- **减少对话上下文大小**：当对话历史变得很长时，使用 `/compact` 可以压缩对话内容，减少令牌使用量。
- **手动压缩控制**：虽然 Claude Code 默认在上下文超过 95% 容量时自动压缩（可通过 `/config` 开启/关闭自动压缩），但你可以使用 `/compact` 手动触发压缩。

所以，为了有效管理成本和性能：

- 建议在上下文变大时定期使用 `/compact` 手动进行压缩；
- 定时使用 `clear` 命令重置上下文；
- 分解复杂任务或者把需求尽量具体化；

### 创建精确的提示

创建精确的 prompt，避免多次交互浪费 token。

## Ref

- 官方文档：<https://docs.anthropic.com/en/docs/claude-code/overview>
