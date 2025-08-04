---
banner: 
date_created: Sunday, July 20th 2025, 12:16:13 am
date_updated: Wednesday, July 30th 2025, 1:08:40 am
title: 04-Claude Code工作流
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
aliases: [Claude Code 工作流]
linter-yaml-title-alias: Claude Code 工作流
---

# Claude Code 工作流

官方：<https://docs.anthropic.com/zh-CN/docs/claude-code/common-workflows>

## 常见工作流程 (官方)

### 理解新代码库

#### 快速获取代码库概览

假设您刚加入一个新项目，需要快速了解其结构。导航到项目根目录，启动 Claude Code

```shell
claude
```

- 请求高级概览

```shell
give me an overview of this codebase
```

- 深入了解特定组件

```shell
> explain the main architecture patterns used here

> what are the key data models?

> how is authentication handled?
```

> - 从广泛的问题开始，然后缩小到特定领域
> - 询问项目中使用的编码约定和模式
> - 请求项目特定术语的词汇表

#### 查找相关代码

假设您需要定位与特定功能或功能相关的代码。

- 让 Claude 查找相关文件

```shell
> find the files that handle user authentication
```

- 了解组件如何交互

```shell
> how do these authentication files work together?
```

- 理解执行流程

```shell
> trace the login process from front-end to database
```

> - 明确说明您要查找的内容
> - 使用项目中的领域语言

### 高效修复错误

假设您遇到了错误消息，需要找到并修复其源头。

- 与 Claude 分享错误

```shell
> I'm seeing an error when I run npm test
```

- 请求修复建议

```shell
> suggest a few ways to fix the @ts-ignore in user.ts
```

- 应用修复

```shell
> update user.ts to add the null check you suggested
```

> - 告诉 Claude 重现问题的命令并获取堆栈跟踪
> - 提及重现错误的任何步骤
> - 让 Claude 知道错误是间歇性的还是持续的

### 重构代码

假设您需要更新旧代码以使用现代模式和实践。

- 识别需要重构的遗留代码

```shell
> find deprecated API usage in our codebase
```

- 获取重构建议

```shell
> suggest how to refactor utils.js to use modern JavaScript features
```

- 安全地应用更改

```shell
> refactor utils.js to use ES2024 features while maintaining the same behavior
```

- 验证重构

```shell
> run tests for the refactored code
```

> - 让 Claude 解释现代方法的好处
> - 在需要时请求更改保持向后兼容性
> - 以小的、可测试的增量进行重构

### 处理测试

假设您需要为未覆盖的代码添加测试。

- 识别未测试的代码

```shell
> find functions in NotificationsService.swift that are not covered by tests
```

- 生成测试脚手架

```shell
> add tests for the notification service
```

- 添加有意义的测试用例

```shell
> add test cases for edge conditions in the notification service
```

- 运行并验证测试

```shell
> run the new tests and fix any failures
```

> - 请求覆盖边缘情况和错误条件的测试
> - 在适当时请求单元测试和集成测试
> - 让 Claude 解释测试策略

### 处理文档

假设您需要为您的代码添加或更新文档。

- 识别未文档化的代码

```shell
> find functions without proper JSDoc comments in the auth module
```

- 生成文档

```shll
> add JSDoc comments to the undocumented functions in auth.js
```

- 审查和增强

```shell
> improve the generated documentation with more context and examples
```

- 验证文档

```shell
> check if the documentation follows our project standards
```

> - 指定您想要的文档样式（JSDoc、docstrings 等）
> - 在文档中请求示例
> - 为公共 API、接口和复杂逻辑请求文档

### 处理图像

假设您需要在代码库中处理图像，并希望 Claude 帮助分析图像内容。

#### 向对话添加图像

您可以使用以下任何方法：

1. 将图像拖放到 Claude Code 窗口中
2. 复制图像并使用 ctrl+v 粘贴到 CLI 中（不要使用 cmd+v）
3. 提供图像路径给 claude。例如："Analyze this image: /path/to/your/image.png"

#### 让 Claude 分析图像

- 让 Claude 分析图像

```shell
> What does this image show?

> Describe the UI elements in this screenshot

> Are there any problematic elements in this diagram?
```

- 使用图像提供上下文

```shell
> Here's a screenshot of the error. What's causing it?

> This is our current database schema. How should we modify it for the new feature?
```

- 从视觉内容获取代码建议

```shell
> Generate CSS to match this design mockup

> What HTML structure would recreate this component?
```

> - 当文本描述不清楚或繁琐时使用图像
> - 包含错误截图、UI 设计或图表以获得更好的上下文
> - 您可以在对话中使用多个图像
> - 图像分析适用于图表、截图、模型图等

### 使用扩展思考

假设您正在处理复杂的架构决策、具有挑战性的错误或需要深度推理的多步骤实现规划。

- 提供上下文并让 Claude 思考

```shell
> I need to implement a new authentication system using OAuth2 for our API. Think deeply about the best approach for implementing this in our codebase.
```

Claude 将从您的代码库收集相关信息并使用扩展思考，这将在界面中可见。

- 通过后续提示完善思考

```shell
> think about potential security vulnerabilities in this approach

> think harder about edge cases we should handle
```

从扩展思考中获得最大价值的提示：

扩展思考对以下复杂任务最有价值：

- 规划复杂的架构更改
- 调试复杂问题
- 为新功能创建实现计划
- 理解复杂的代码库
- 评估不同方法之间的权衡

您提示思考的方式会导致不同深度的思考：

- "`think`" 触发基本扩展思考
- 强化短语如 "`think more`"、"`think a lot`"、"`think harder`" 或 "`think longer`" 触发更深层的思考

有关更多**扩展思考**提示技巧，请参阅 [延展思考技巧 - Anthropic](https://docs.anthropic.com/zh-CN/docs/build-with-claude/prompt-engineering/extended-thinking-tips)

> Claude 将在响应上方以斜体灰色文本显示其思考过程。

### 恢复之前的对话

假设您一直在使用 Claude Code 处理任务，需要在稍后的会话中从中断的地方继续。

Claude Code 提供两个选项来恢复之前的对话：

- `--continue` 自动继续最近的对话
- `--resume` 显示对话选择器
- **继续最近的对话**：这会立即恢复您最近的对话，无需任何提示

```shell
claude --continue
```

- **在非交互模式下继续**

```shell
claude --continue --print "Continue with my task"
```

使用 `--print` 与 `--continue` 在非交互模式下恢复最近的对话，非常适合脚本或自动化。

- **显示对话选择器**

```shell
claude --resume
```

这会显示一个交互式对话选择器，显示：

- 对话开始时间
- 初始提示或对话摘要
- 消息数量

使用箭头键导航并按 Enter 选择对话。

**提示：**
- 对话历史存储在您的本地机器上
- 使用 `--continue` 快速访问您最近的对话
- 当您需要选择特定的过去对话时使用 `--resume`
- 恢复时，您将在继续之前看到整个对话历史
- 恢复的对话以与原始对话相同的模型和配置开始

**工作原理：**
1. **对话存储**：所有对话都会自动保存在本地，包含完整的消息历史
2. **消息反序列化**：恢复时，整个消息历史被恢复以保持上下文
3. **工具状态**：之前对话中的工具使用和结果得到保留
4. **上下文恢复**：对话恢复时保持所有之前的上下文完整

**示例：**

```shell
# 继续最近的对话
claude --continue

# 使用特定提示继续最近的对话
claude --continue --print "Show me our progress"

# 显示对话选择器
claude --resume

# 在非交互模式下继续最近的对话
claude --continue --print "Run the tests again"
```

### 将 Claude 用作 unix 风格的实用程序

### 将 Claude 添加到您的验证过程（lint/code review）

假设您想将 Claude Code 用作 linter 或 code review 器。

**将 Claude 添加到您的构建脚本：**

```json
// package.json
{
    …
    "scripts": {
        …
        "lint:claude": "claude -p 'you are a linter. please look at the changes vs. main and report any issues related to typos. report the filename and line number on one line, and a description of the issue on the second line. do not return any other text.'"
    }
}
```

**提示：**
- 在您的 CI/CD 管道中使用 Claude 进行自动化代码审查
- 自定义提示以检查与您项目相关的特定问题
- 考虑为不同类型的验证创建多个脚本

#### 管道输入，管道输出

假设您想将数据管道输入到 Claude，并以结构化格式获取数据。

**通过 Claude 管道数据：**

```shell
cat build-error.txt | claude -p 'concisely explain the root cause of this build error' > output.txt
```

**提示：**
- 使用管道将 Claude 集成到现有的 shell 脚本中
- 与其他 Unix 工具结合使用以实现强大的工作流程
- 考虑使用 `—output-format` 进行结构化输出

#### 控制输出格式

假设您需要 Claude 的输出采用特定格式，特别是在将 Claude Code 集成到脚本或其他工具中时。

- **使用文本格式（默认）**: 这只输出 Claude 的纯文本响应（默认行为）。

```shell
cat data.txt | claude -p 'summarize this data' --output-format text > summary.txt
```

- **使用 JSON 格式**: 这输出包含成本和持续时间等元数据的消息 JSON 数组。

```shell
cat code.py | claude -p 'analyze this code for bugs' --output-format json > analysis.json
```

- **使用流式 JSON 格式**:

```shell
cat log.txt | claude -p 'parse this log file for errors' --output-format stream-json
```

这在 Claude 处理请求时实时输出一系列 JSON 对象。每条消息都是有效的 JSON 对象，但如果连接，整个输出不是有效的 JSON。

**提示：**
- 对于只需要 Claude 响应的简单集成，使用 `--output-format text`
- 当您需要完整的对话日志时，使用 `--output-format json`
- 对于每个对话轮次的实时输出，使用 `--output-format stream-json`

### 创建自定义斜杠命令

Claude Code 支持自定义斜杠命令，您可以创建这些命令来快速执行特定的提示或任务。

### Git

#### 创建 Merge Request

- 总结您的更改

```shell
> summarize the changes I've made to the authentication module
```

- 使用 Claude 生成 PR

```shell
> create a pr
```

- 审查和完善

```shell
> enhance the PR description with more context about the security improvements
```

- 添加测试详情

```shell
> add information about how these changes were tested
```

> - 直接让 Claude 为您创建 PR
> - 在提交前审查 Claude 生成的 PR
> - 让 Claude 突出潜在的风险或考虑因素

#### 使用 Git worktrees 运行并行 Claude Code 会话

假设您需要同时处理多个任务，并在 Claude Code 实例之间完全隔离代码。

- 理解 Git worktrees
Git worktrees 允许您将同一存储库的多个分支检出到单独的目录中。每个 worktree 都有自己的工作目录和隔离的文件，同时共享相同的 Git 历史。在 [官方 Git worktree 文档](https://git-scm.com/docs/git-worktree) 中了解更多。

- 创建新的 worktree

```shell
# 使用新分支创建新的 worktree
git worktree add ../project-feature-a -b feature-a

# 或使用现有分支创建 worktree
git worktree add ../project-bugfix bugfix-123
```

这会创建一个新目录，其中包含您存储库的单独工作副本。

- 在每个 worktree 中运行 Claude Code

```shell
# 导航到您的 worktree
cd ../project-feature-a

# 在这个隔离环境中运行 Claude Code
claude
```

- 在另一个 worktree 中运行 Claude

```shell
cd ../project-bugfix
claude
```

- 管理您的 worktrees

```shell
# 列出所有 worktrees
git worktree list

# 完成后删除 worktree
git worktree remove ../project-feature-a
```

**提示：**
- 每个 worktree 都有自己独立的文件状态，非常适合并行 Claude Code 会话
- 在一个 worktree 中所做的更改不会影响其他 worktree，防止 Claude 实例相互干扰
- 所有 worktrees 共享相同的 Git 历史和远程连接
- 对于长时间运行的任务，您可以让 Claude 在一个 worktree 中工作，同时在另一个 worktree 中继续开发
- 使用描述性目录名称轻松识别每个 worktree 用于哪个任务
- 记住根据项目的设置在每个新 worktree 中初始化您的开发环境。根据您的技术栈，这可能包括：
	- JavaScript 项目：运行依赖安装（`npm install`、`yarn`）
	- Python 项目：设置虚拟环境或使用包管理器安装
	- 其他语言：遵循项目的标准设置过程

## 规范驱动开发工作流

### 规范驱动开发

类似亚马逊 PRD 的开发方式，通过计划模式（Plan Mode）和任务分解来确保 Claude Code 的工作有条理、高效：

· **计划模式 (plan)**：  
  在开始编码前，始终进入计划模式。在这个模式下，Claude Code 会专注于需求分析、架构设计和查阅最新技术文档，而不会直接修改代码。Jason 建议与 AI 反复沟通，完善计划后再进入实施阶段。

· **任务分解与跟踪 (design)**：  
  Claude Code 会将大任务拆分成小任务，保存在 `doc/claude/task/taskname.md` 文件中。每次执行任务时，它会更新这个文件，记录完成的步骤和细节。这种方式让开发过程清晰可追溯，类似 " 文档驱动 " 的开发流程。

· **To-Do 工具 (task)**：  
  Claude Code 使用内置的 To-Do 工具来管理任务列表，每个任务包含内容、ID、优先级和状态。Jason 发现这种简单设计非常有效，AI 会根据任务列表逐一执行，并自动更新状态。

### kiro 开发工作流 Spec

见 [[Spec工作流]]

## 开源
