---
banner: 
date_created: Thursday, July 31st 2025, 12:20:24 am
date_updated: Thursday, July 31st 2025, 11:37:23 pm
title: Claude Code Sub Agents
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
aliases: [Claude Code Sub Agents]
linter-yaml-title-alias: Claude Code Sub Agents
---

# Claude Code Sub Agents

## Sub Agents 基础

<https://docs.anthropic.com/en/docs/claude-code/sub-agents>

### 什么是 Sub Agent？

Sub Agents 本质上是预配置的专业 AI 助手，它们能够被 Claude Code 主系统委托处理特定类型的任务。每个 Sub Agent 都拥有独立的上下文窗口、定制化的系统提示词以及特定的工具访问权限。这种设计使得每个 Sub Agent 都能专注于自己的专业领域，如代码审查、调试或数据分析等。

与传统的单一 AI 助手不同，Sub Agents 采用了 " 术业有专攻 " 的理念。当 Claude Code 遇到匹配某个 Sub Agent 专业领域的任务时，会自动将任务委托给相应的专业 Sub Agent 处理，从而获得更精准、更专业的结果。每个 Sub Agent：

- 有明确分工：专注于某一类任务，比如检查代码质量或分析数据
- 独立工作空间：有自己的 " 记忆窗口 "，不会干扰你主会话的上下文
- 自定义配置：你可以设定它们的任务描述、可用工具和行为规则
- 分工协作：Claude Code 会根据任务需要，自动把工作交给合适的 Sub Agent，或者你也可以手动指定

### 为什么用 Sub Agent？

Sub Agent 就像请了一群专家来帮你干活，带来这些好处：

1. 保持主线清晰：Sub Agent 有独立的上下文，不会把你的主对话搞乱，让你能专注于总体目标
2. 专业性强：每个 Sub Agent 可以针对特定任务深度定制，干活更靠谱
3. 可重复使用：做好一个 Sub Agent，可以在不同项目里反复用，还能分享给团队
4. 权限灵活：你能控制每个 Sub Agent 能用哪些工具，保障安全和专注

### 开始生成 agents

- 打开 Sub Agents 界面

```shell
/agents
```

- Select 'Create New Agent'
![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250731002218096.png)

- 选择在项目还是用户目录
- 接着选择由 Claude 帮忙创建，然后自己再去修改
- 输入提示词：

```shell
> Use the code-reviewer sub agent to check my recent changes
```

### Sub agent configuration

#### agent 文件路径

| Type                   | Location            | Scope                         | Priority |
| ---------------------- | ------------------- | ----------------------------- | -------- |
| **Project sub agents** | `.claude/agents/`   | Available in current project  | Highest  |
| **User sub agents**    | `~/.claude/agents/` | Available across all projects | Lower    |

#### File format

每个 sub agent 通过一个 markdown 文件定义

```markdown
---
name: your-sub-agent-name
description: Description of when this sub agent should be invoked
tools: tool1, tool2, tool3  # Optional - inherits all tools if omitted
---

Your sub agent's system prompt goes here. This can be multiple paragraphs
and should clearly define the sub agent's role, capabilities, and approach
to solving problems.

Include specific instructions, best practices, and any constraints
the sub agent should follow.
```

**Configuration fields：**

| Field         | Required | Description                                                                                                               |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------------------- |
| `name`        | Yes      | Unique identifier using lowercase letters and hyphens 必填，使用小写字母和连字符的唯一标识符                                                 |
| `description` | Yes      | Natural language description of the sub agent's purpose 必填，自然语言描述 Sub Agent 的用途                                           |
| `tools`       | No       | Comma-separated list of specific tools. If omitted, inherits all tools from the main thread 可选，逗号分隔的工具列表。如果省略，则继承主线程的所有工具 |

#### Available tools

Sub agent 可以访问 Claude Code [内部的工具](https://docs.anthropic.com/en/docs/claude-code/settings#tools-available-to-claude)。如

```shell
# 文件操作
file_read, file_write, file_delete

# 终端和搜索
terminal, search_files, search_code

# Git 操作
git_commit, git_push, git_branch

# 构建工具
docker_build, docker_run, docker_compose
npm_install, pip_install, cargo_build

# 数据库
database_query, redis_command

# 网络
http_request, curl_command
```

有两种方式配置 tools:

- 指定 `tools` 字段，从 main 继承，包括 mcp tools
- 指定个人 tools

需要注意的是，Sub Agents 也能访问配置的 MCP（Model Context Protocol）服务器工具。

当省略 `tools` 字段时，Sub Agent 会继承主线程可用的所有 MCP 工具。

#### 怎么管理 Sub Agent？

##### 用 `/agents` 命令

推荐方式：用 `/agents` 命令，界面友好，能：

- 查看所有 Sub Agent（内置的、用户的、项目的）
- 创建、编辑、删除 Sub Agent
- 调整工具权限，界面会列出所有可用工具（包括 MCP 服务器的工具）

##### 手动方式

直接改配置文件，比如在终端里创建：

```shell
mkdir -p .claude/agents
echo '---
name: test-runner
description: 自动跑测试并修复问题
---
```

你是一个测试专家，看到代码变化就主动跑测试……' > .claude/agents/test-runner. md

### 高效使用 Sub Agents

#### 自动代理

Claude Code 主动代理，基于：

- 任务的描述
- sub agents 的 `description` 字段
- 当前 Context 和可用的 tools

为了鼓励 sub agents 使用，可以包含类似 `use PROACTIVELY` 或 `MUST BE USED` 在你的 `description` 字段中

#### 显示调用

在命令中提及特定 sub agent，以请求该 sub agent

```shell
> Use the test-runner sub agent to fix failing tests
> Have the code-reviewer sub agent look at my recent changes
> Ask the debugger sub agent to investigate this error
```

### Sub Agents 示例

#### Code reviewer

<https://docs.anthropic.com/en/docs/claude-code/sub-agents#code-reviewer>

```markdown
---
name: code-reviewer
description: Expert code review specialist. Proactively reviews code for quality, security, and maintainability. Use immediately after writing or modifying code.
tools: Read, Grep, Glob, Bash
---

You are a senior code reviewer ensuring high standards of code quality and security.

When invoked:
1. Run git diff to see recent changes
2. Focus on modified files
3. Begin review immediately

Review checklist:
- Code is simple and readable
- Functions and variables are well-named
- No duplicated code
- Proper error handling
- No exposed secrets or API keys
- Input validation implemented
- Good test coverage
- Performance considerations addressed

Provide feedback organized by priority:
- Critical issues (must fix)
- Warnings (should fix)
- Suggestions (consider improving)

Include specific examples of how to fix issues.
```

#### Debugger

<https://docs.anthropic.com/en/docs/claude-code/sub-agents#debugger>

```markdown
---
name: debugger
description: Debugging specialist for errors, test failures, and unexpected behavior. Use proactively when encountering any issues.
tools: Read, Edit, Bash, Grep, Glob
---

You are an expert debugger specializing in root cause analysis.

When invoked:
1. Capture error message and stack trace
2. Identify reproduction steps
3. Isolate the failure location
4. Implement minimal fix
5. Verify solution works

Debugging process:
- Analyze error messages and logs
- Check recent code changes
- Form and test hypotheses
- Add strategic debug logging
- Inspect variable states

For each issue, provide:
- Root cause explanation
- Evidence supporting the diagnosis
- Specific code fix
- Testing approach
- Prevention recommendations

Focus on fixing the underlying issue, not just symptoms.
```

#### Data scientist

<https://docs.anthropic.com/en/docs/claude-code/sub-agents#data-scientist>

```markdown
---
name: data-scientist
description: Data analysis expert for SQL queries, BigQuery operations, and data insights. Use proactively for data analysis tasks and queries.
tools: Bash, Read, Write
---

You are a data scientist specializing in SQL and BigQuery analysis.

When invoked:
1. Understand the data analysis requirement
2. Write efficient SQL queries
3. Use BigQuery command line tools (bq) when appropriate
4. Analyze and summarize results
5. Present findings clearly

Key practices:
- Write optimized SQL queries with proper filters
- Use appropriate aggregations and joins
- Include comments explaining complex logic
- Format results for readability
- Provide data-driven recommendations

For each analysis:
- Explain the query approach
- Document any assumptions
- Highlight key findings
- Suggest next steps based on data

Always ensure queries are efficient and cost-effective.
```

#### API 测试生成器

```markdown
---
name: api-test-generator
description: 生成全面的 API 测试，包括单元测试、集成测试和负载测试。在处理 API 端点或微服务时主动使用。
tools: file_read, file_write, terminal, search_files
---

你是 API 测试专家，专注于创建健壮的测试套件。你的职责：

**测试生成：**
- 单个端点的单元测试
- API 工作流的集成测试
- 服务边界的契约测试
- 负载和性能测试
- 身份验证/授权的安全测试

**测试框架：**
- Node.js API 使用 Jest/Mocha
- Python API 使用 pytest
- Java API 使用 JUnit
- 集成测试使用 Postman/Newman

**覆盖要求：**
- 正常路径场景
- 边缘情况和错误条件
- 输入验证测试
- 速率限制验证
- 身份验证/授权测试

始终生成遵循 AAA 模式（安排、执行、断言）的测试，并包含有意义的断言。
```

#### 数据库迁移专家

```markdown
---
name: db-migration-specialist
description: 创建和管理数据库迁移、模式更改和数据转换。用于任何数据库模式修改或数据迁移任务。
tools: file_read, file_write, terminal, search_files
---

你是数据库迁移专家，具有以下专长：

**迁移创建：**
- 模式更改（表、列、索引、约束）
- 数据转换和迁移
- 性能优化的迁移脚本
- 回滚程序

**数据库系统：**
- PostgreSQL、MySQL、SQLite
- MongoDB（文档迁移）
- Redis（数据结构迁移）

**最佳实践：**
- 具有适当事务的原子迁移
- 向后兼容性考虑
- 性能影响评估
- 数据完整性验证
- 全面的回滚程序

始终包含向上和向下迁移脚本、性能估计和数据验证步骤。
```

### Sub Agents 最佳实践

- **Start with Claude-generated agents**
由 Claude Code 初始化 sub agents，再自己补充细节

- **Design focused sub agents**
创建职责明确、单一的 sub agent，而不是让一个 sub agent 包揽所有事情。这样可以提高性能，并使 sub agent 的工作更具可预测性

- **Write detailed prompts**
在系统提示中包含具体的 `instructions`、`examples` 和 `constraints`。您提供的指导越多，sub agent 的表现就越好。

- **Limit tool access**
仅授予 sub agent 执行其任务所需的工具。这可以提高安全性，并帮助 sub agents 专注于相关操作

- **Version control**
将项目 sub agent 检查到版本控制中，以便您的团队可以从中受益并协作改进。

### Sub Agents 高级用法

#### Chaining sub agents

针对复杂的工作流，可以链接多个子代理

```shell
> First use the code-analyzer sub agent to find performance issues, then use the optimizer sub agent to fix them
```

#### Dynamic sub agent selection

Claude Code 会根据 `context` 智能地选择 `sub agents`。为了获得最佳效果，请确保您提供的 `description` 字段具体且以行动为导向。

#### Performance considerations

- **Context efficiency**
Agents 有助于保留 main context，从而延长整体会话时间

- **Latency**
Sub agents 每次被调用时都是从一张白纸开始，并且在收集有效完成工作所需的上下文时可能会增加延迟。

## Sub Agents 开源

### 三方的

- <https://www.claudecodeagents.com/>
- <https://subagents.cc/>

### contains-studio/agents

<https://github.com/contains-studio/agents>

**安装：**

```shell
git clone https://github.com/contains-studio/agents.git

cp -r agents/* ~/.claude/agents/
```

### iannuttall/claude-agents 7 个 Agents

<https://github.com/iannuttall/claude-agents> 提供了优化代码、写文档、设计前端界面、规划项目任务、安全审计等 7 个 Agents。

**安装：**

```shell
# 项目级
mkdir -p .claude/agents
cp agents/*.md .claude/agents/

# 全局
mkdir -p ~/.claude/agents
cp agents/*.md ~/.claude/agents/
```

**可用的 Agents：**
- **code-refactorer**: Code refactoring assistance
- **content-writer**: Content writing assistance
- **frontend-designer**: Frontend design assistance
- **prd-writer**: Product requirement document writing
- **project-task-planner**: Project planning and task breakdown
- **security-auditor**: Security audit assistance
- **vibe-coding-coach**: Coding guidance and coaching
