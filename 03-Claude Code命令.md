---
banner: 
date_created: Tuesday, July 15th 2025, 12:20:55 am
date_updated: Tuesday, July 22nd 2025, 1:06:15 am
title: 03-Claude Code命令
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
aliases: [Claude Code Slash Command]
linter-yaml-title-alias: Claude Code Slash Command
---

# Claude Code Slash Command

## Claude Code 命令

官方文档：<https://docs.anthropic.com/zh-CN/docs/claude-code/slash-commands#%E8%87%AA%E5%AE%9A%E4%B9%89%E6%96%9C%E6%9D%A0%E5%91%BD%E4%BB%A4>

## Slash Command 概念

在软件开发的日常工作中，重复性任务和复杂流程总是消耗着开发者大量的时间和精力。Claude Code 的 `/` 命令（Slash Command）为这个问题提供了一个优雅的解决方案——通过简洁的命令接口，将常用的开发流程和工作流自动化，让开发者能够专注于真正重要的创造性工作。

斜杠命令是 Claude Code 的核心交互方式，它提供了一种结构化的方法来控制 AI 助手的行为。通过简洁的 `/command` 语法，你可以快速访问预定义的功能，或行自定义的复杂工作流。

这种设计哲学借鉴了现代开发工具的最佳实践：

- **简洁性**：单一命令完成复杂任务。
- **一致性**：统一的命令格式和参数传递方式。
- **可扩展性**：支持自定义命令和团队协作。
- **上下文感知**：命令能够理解当前项目状态和开发环境。

## 内置基础命令

Claude Code 提供了丰富的内置命令，覆盖了开发工作流的各个环节。

### 常用命令

日常使用最重要的命令：

| 命令                  | 功能             | 示例                                  |
| ------------------- | -------------- | ----------------------------------- |
| `claude`            | 启动交互模式         | `claude`                            |
| `claude "task"`     | 运行一次性任务        | `claude "fix the build error"`      |
| `claude -p "query"` | 运行一次性查询，然后退出   | `claude -p "explain this function"` |
| `claude -c`         | 继续最近的对话        | `claude -c`                         |
| `claude -r`         | 恢复之前的对话        | `claude -r`                         |
| `claude commit`     | 创建 Git 提交      | `claude commit`                     |
| `/clear`            | 清除对话历史         | `> /clear`                          |
| `/help`             | 显示可用命令         | `> /help`                           |
| `exit` 或 Ctrl+C     | 退出 Claude Code | `> exit`                            |
| `/status`           | 查看状态           | `> /status`                         |

### 账户与环境管理

这类命令帮助你管理 Claude Code 的基本配置和工作环境：

| 命令             | 功能描述               | 使用场景        |
| -------------- | ------------------ | ----------- |
| `/login`       | 切换或登录 Anthropic 账户 | 多账户管理，团队协作  |
| `/logout`      | 退出当前账户             | 安全清理，账户切换   |
| `/config`      | 查看和修改配置设置          | 环境配置，偏好设置   |
| `/permissions` | 管理工具权限             | 安全配置，权限控制   |
| `/status`      | 查看系统和账户状态          | 健康检查，故障诊断   |
| `/model`       | 选择或更改 AI 模型        | 性能调优，成本控制   |
| `/mcp`         | 管理 MCP 服务器连接       | 第三方集成，企业工具链 |

### 项目与内存管理

这些命令专注于项目层面的配置和上下文管理：

| 命令                        | 功能描述                 | 使用场景          |
| ------------------------- | -------------------- | ------------- |
| `/init`                   | 初始化项目并创建 `CLAUDE.md` | 新项目开始，团队标准化   |
| `/memory`                 | 编辑项目记忆文件             | 项目文档更新，上下文优化  |
| `/add-dir`                | 添加额外工作目录             | 多模块项目，跨目录操作   |
| `/clear`                  | 清除对话历史               | 重新开始，节省 token |
| `/compact [instructions]` | 压缩对话历史               | 长对话优化，上下文管理   |

- `/init` 项目初始化命令：

```shell
/init
# 也可以用来覆盖更新
/init --force
```

Claude 会自动分析项目结构并生成 `CLAUDE.md` 项目说明文档（可附加 " 请生成中文 " 的指令）。

### 开发与调试工具

核心的开发辅助命令，提升日常编码效率：

| 命令             | 功能描述            | 使用场景        |
| -------------- | --------------- | ----------- |
| `/review`      | 请求代码审查          | 代码质量保证，同行评审 |
| `/bug`         | 报告问题给 Anthropic | 问题反馈，产品改进   |
| `/doctor`      | 检查安装健康状态        | 环境诊断，故障排查   |
| `/pr_comments` | 查看拉取请求评论        | 代码评审流程，团队协作 |
| `/vim`         | 进入 vim 编辑模式     | 高效文本编辑，键盘操作 |

### 监控与分析

帮助你了解使用情况和优化工作流：

| 命令      | 功能描述          | 使用场景      |
| ------- | ------------- | --------- |
| `/cost` | 显示 token 使用统计 | 成本监控，使用优化 |
| `/help` | 获取帮助信息        | 学习使用，功能发现 |

## 自定义斜杠命令

自定义斜杠命令是 Claude Code 最强大的特性之一，它让你能够将任何复杂的开发流程封装成简单的命令。这种能力不仅提升了个人效率，更重要的是能够在团队中标准化和共享最佳实践。

**一个简单示例：**
在 `.claude/commands/` 目录中添加：

```shell
.claude/commands/lintfix.prompt
```

内容如下：

```shell
Read the currently open file in my IDE and identify all linter warnings.
Then, apply the necessary changes to fix all of them automatically.
Provide a summary of the changes you made.
```

使用：

```shell
/lintfix
```

### 命令类型（作用域）

自定义命令采用了分层架构，支持两种作用域：

```shell
项目命令 (.claude/commands/)     个人命令 (~/.claude/commands/)
        ↓                              ↓
   /project:command                /user:command
        ↓                              ↓
   团队共享，版本控制              个人定制，跨项目复用
```

### 命名空间

此外，你还可以通过目录结构创建命名空间，实现命令的逻辑分组：

**结构**: `<prefix>:<namespace>:<command>`

例如，位于 `.claude/commands/frontend/component.md` 的文件创建命令 `/project:frontend:component`

**示例：**

```shell
.claude/commands/
├── frontend/
│   ├── component.md        # /project:frontend:component
│   ├── style-guide.md      # /project:frontend:style-guide
│   └── testing.md          # /project:frontend:testing
├── backend/
│   ├── api-design.md       # /project:backend:api-design
│   ├── database.md         # /project:backend:database
│   └── security.md         # /project:backend:security
└── devops/
    ├── deploy.md           # /project:devops:deploy
    ├── monitoring.md       # /project:devops:monitoring
    └── backup.md           # /project:devops:backup
```

这种组织方式让大型项目的命令管理变得井然有序，团队成员可以快速找到所需的命令。

### $ARGUMENTS 添加命令参数

使用 `$ARGUMENTS` 占位符传入参数，支持复杂的参数传递模式：

**示例 1：**
- 创建带有 `$ARGUMENTS` 占位符的命令文件: `.claude/commands/fix-issue.md`

```markdown
echo "Find and fix issue #$ARGUMENTS. Follow these steps: 
1. Understand the issue described in the ticket 
2. Locate the relevant code in our codebase
3. Implement a solution that addresses the root cause
4. Add appropriate tests
5. Prepare a concise PR description" 
```

- 使用带有问题编号的命令: 在您的 Claude 会话中，使用带参数的命令。

```shell
> /project:fix-issue 123
# 这将在提示中将 $ARGUMENTS 替换为 “123”。
```

**提示：**
- `$ARGUMENTS` 占位符被命令后面的任何文本替换
- 您可以在命令模板中的任何位置放置 `$ARGUMENTS`
- 其他有用的应用：为特定函数生成测试用例、为组件创建文档、审查特定文件中的代码或将内容翻译为指定语言

**示例 2：**

```markdown
---  
description: 智能代码重构工具  
allowed-tools: Read, Edit, Bash(git*)  
---  
  
# 重构任务：$ARGUMENTS  
  
## 重构策略  
  
根据传入的参数类型执行不同的重构策略：  
  
**如果是文件路径** ：分析文件结构，提供重构建议  
**如果是函数名** ：查找函数定义，优化实现逻辑  
**如果是模块名** ：审查模块接口，改进 API 设计  
  
## 执行前检查  
  
当前工作区状态：!`git status --porcelain`  
代码覆盖率：!`npm run test:coverage 2>/dev/null | grep -E '[0-9]+%' || echo "无测试覆盖率数据"`  
  
## 安全保障  
  
- 自动创建备份分支  
- 执行测试验证重构结果  
- 提供回滚方案
```

### Bash 命令执行

使用 `!` 前缀在斜杠命令运行之前执行 bash 命令。输出包含在命令上下文中。

例如：

```
---
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git commit:*)
description: 创建 git 提交
---

## 上下文

- 当前 git 状态：!`git status`
- 当前 git diff（已暂存和未暂存的更改）：!`git diff HEAD`
- 当前分支：!`git branch --show-current`
- 最近的提交：!`git log --oneline -10`

## 您的任务

基于上述更改，创建单个 git 提交。
```

### 文件引用

使用 `@` 前缀在命令中包含文件内容以 [引用文件](https://docs.anthropic.com/zh-CN/docs/claude-code/common-workflows#reference-files-and-directories)。

例如：

```
# 引用特定文件
审查 @src/utils/helpers.js 中的实现

# 引用多个文件
比较 @src/old-version.js 与 @src/new-version.js
```

### 思考模式

斜杠命令可以通过包含 [扩展思考关键词](https://docs.anthropic.com/zh-CN/docs/claude-code/common-workflows#use-extended-thinking) 来触发扩展思考。

### 文件格式

命令文件支持：

- **Markdown 格式**（`.md` 扩展名）
- **YAML 前言**用于元数据：
	- `allowed-tools`：命令可以使用的工具列表
	- `description`：命令的简要描述
- **动态内容**，包含 bash 命令（`!`）和文件引用（`@`）
- **提示指令** 作为主要内容

### 上下文感知机制

通过预执行命令和文件引用，命令能够智能地理解当前开发环境：

```
---  
description: 智能问题诊断工具  
allowed-tools: Bash(*), Read, Grep  
---  
  
# 问题诊断：$ARGUMENTS  
  
## 环境信息收集  
  
** 系统环境 **：  
- Node.js 版本：!`node --version`  
- NPM 版本：!`npm --version`  
- Git 状态：!`git status --short`  
  
** 项目状态 **：  
- 包信息：@package.json  
- 配置文件：@.env.example  
- 最近错误日志：!`tail -20 logs/error.log 2>/dev/null || echo "无错误日志"`  
  
** 代码分析 **：  
- 依赖检查：!`npm ls --depth=0 2>&1 | grep -E "WARN|ERR" || echo "依赖状态正常"`  
- 类型检查：!`npx tsc --noEmit 2>&1 | head -10 || echo "无 TypeScript 错误"`  
  
基于以上信息，请分析问题 "$ARGUMENTS" 的可能原因并提供解决方案。
```

### 创建项目特定命令

假设您想为您的项目创建可重用的斜杠命令，所有团队成员都可以使用。

- 在您的项目中创建命令目录

```shell
mkdir -p .claude/commands
```

- 为每个命令创建 Markdown 文件

```shell
echo "Analyze the performance of this code and suggest three specific optimizations:" > .claude/commands/optimize.md
```

- 在 Claude Code 中使用您的自定义命令

```shell
> /project:optimize
```

**提示：**
- 命令名称来自文件名（例如，`optimize.md` 变成 `/project:optimize`）
- 您可以在子目录中组织命令（例如，`.claude/commands/frontend/component.md` 变成 `/project:frontend:component`）
- 项目命令对克隆存储库的每个人都可用
- Markdown 文件内容成为调用命令时发送给 Claude 的提示

### 创建个人斜杠命令

假设您想创建在所有项目中都有效的个人斜杠命令。

- 在您的主文件夹中创建命令目录

```shell
mkdir -p ~/.claude/commands
```

- 为每个命令创建 Markdown 文件

```shell
echo "Review this code for security vulnerabilities, focusing on:" >
~/.claude/commands/security-review.md
```

- 使用您的个人自定义命令

```shell
> /user:security-review
```

**提示：**
- 个人命令以 `/user:` 而不是 `/project:` 为前缀
- 个人命令只对您可用，不与您的团队共享
- 个人命令在您的所有项目中都有效
- 您可以使用这些命令在不同代码库中保持一致的工作流程

## MCP 斜杠命令

MCP 服务器可以将提示公开为斜杠命令，这些命令在 Claude Code 中变为可用。这些命令从连接的 MCP 服务器动态发现。

### 命令格式

MCP 命令遵循以下模式：

```shell
/mcp__<server-name>__<prompt-name> [arguments]
```

### 功能

#### 动态发现

MCP 命令在以下情况下自动可用：

- MCP 服务器已连接并处于活动状态
- 服务器通过 MCP 协议公开提示
- 在连接期间成功检索提示

#### 参数

MCP 提示可以接受服务器定义的参数：

```shell
# 不带参数
> /mcp__github__list_prs

# 带参数
> /mcp__github__pr_review 456
> /mcp__jira__create_issue "Bug title" high
```

#### 命名约定

- 服务器和提示名称已标准化
- 空格和特殊字符变为下划线
- 名称小写以保持一致性

### 管理 MCP 连接

使用 `/mcp` 命令来：

- 查看所有配置的 MCP 服务器
- 检查连接状态
- 使用启用 OAuth 的服务器进行身份验证
- 清除身份验证令牌
- 查看每个服务器的可用工具和提示

## 自定义斜杠命令实战

### 偏前端

#### 全栈开发命令

- 命令

```shell
mkdir -p .claude/commands/fullstack  
cat > .claude/commands/fullstack/feature.md <<EOF  
```

- md 内容

```
---  
description: 全栈功能开发流程  
allowed-tools: Read, Edit, MultiEdit, Bash(*)  
---  
  
# 全栈功能开发：$ARGUMENTS  
  
## 开发阶段  
  
### 1. 需求分析  
- 功能规格：从产品文档或用户故事中提取需求  
- 技术方案：设计 API 接口和数据模型  
- 测试策略：定义测试用例和验收标准  
  
### 2. 后端开发  
- 数据模型：@src/models/  
- API 路由：@src/routes/  
- 中间件：@src/middleware/  
- 测试用例：@src/tests/  
  
### 3. 前端开发  
- 组件设计：@src/components/  
- 状态管理：@src/store/  
- 页面逻辑：@src/pages/  
- 样式实现：@src/styles/  
  
### 4. 集成测试  
- API 测试：!`npm run test:api`  
- 前端测试：!`npm run test:frontend`  
- 端到端测试：!`npm run test:e2e`  
  
## 代码质量保证  
  
** 静态分析 **：  
- ESLint 检查：!`npx eslint src/ --format compact`  
- 类型检查：!`npx tsc --noEmit`  
- 测试覆盖率：!`npm run test:coverage | grep -E "Lines|Functions|Branches"`  
  
请按照以上流程实现功能："$ARGUMENTS"，确保代码质量和测试覆盖率。  
EOF
```

#### 安全审查命令

- 命令

```shell
mkdir -p ~/.claude/commands/security  
cat > ~/.claude/commands/security/audit.md <<EOF
```

- md

```
---  
description: 全面安全审查工具  
allowed-tools: Bash(*), Read, Grep  
---  
  
# 安全审查：$ARGUMENTS  
  
## 安全检查维度  
  
### 1. 代码安全  
** 敏感信息泄露 **：  
- 硬编码密钥：!`rg -i "password|secret|key|token" --type js --type ts | head -20`  
- 调试信息：!`rg "console\.(log|debug|info)" --type js --type ts | wc -l`  
  
** 输入验证 **：  
- SQL 注入风险：!`rg "SELECT.*\$\{|INSERT.*\$\{" --type js --type sql | head -10`  
- XSS 风险：!`rg "innerHTML|dangerouslySetInnerHTML" --type js --type tsx | head -10`  
  
### 2. 依赖安全  
** 漏洞扫描 **：  
- NPM 审计：!`npm audit --audit-level=high`  
- 过期依赖：!`npm outdated`  
  
### 3. 配置安全  
** 环境配置 **：  
- 环境变量：@.env.example  
- 配置文件：@config/  
- 权限设置：!`find . -name "*.json" -o -name "*.yaml" | xargs grep -l "auth\|permission"`  
  
### 4. 网络安全  
**HTTPS 配置 **：!`grep -r "http://" src/ config/ | head -10`  
**CORS 设置 **：!`grep -r "cors\|Access-Control" src/ | head -5`  
  
## 安全建议  
  
根据以上检查结果，提供具体的安全改进建议和修复方案。  
EOF
```

#### 代码评审命令

- 命令

```shell
cat > .claude/commands/review/comprehensive.md <<EOF
```

- md

```
---  
description: 全面代码评审流程  
allowed-tools: Bash(git*), Read, Grep, Edit  
---  
  
# 代码评审：$ARGUMENTS  
  
## 评审检查清单  
  
### 1. 功能正确性  
- [ ] 实现是否符合需求规格  
- [ ] 边界条件处理是否完善  
- [ ] 错误处理是否健壮  
- [ ] 业务逻辑是否正确  
  
### 2. 代码质量  
** 可读性 **：  
- 命名是否清晰表达意图  
- 代码结构是否逻辑清晰  
- 注释是否恰当和准确  
  
** 可维护性 **：  
- 函数职责是否单一  
- 模块耦合度是否合理  
- 代码复用是否恰当  
  
### 3. 性能考虑  
** 算法效率 **：  
- 时间复杂度是否合理  
- 空间复杂度是否优化  
- 数据结构选择是否恰当  
  
** 资源使用 **：  
- 内存使用是否合理  
- 网络请求是否优化  
- 数据库查询是否高效  
  
### 4. 安全性检查  
** 输入验证 **：  
- 用户输入是否验证  
- SQL 注入风险评估  
- XSS 攻击防护  
  
** 权限控制 **：  
- 访问权限是否正确  
- 敏感操作是否保护  
- 数据权限是否隔离  
  
## 变更分析  
  
**Git 变更信息 **：  
- 修改文件：!`git diff --name-only HEAD~1 HEAD`  
- 代码行数：!`git diff --stat HEAD~1 HEAD`  
- 提交信息：!`git log -1 --pretty=format:"%s%n%b"`  
  
** 影响范围分析 **：  
- 修改的模块和函数  
- 相关测试用例  
- 依赖关系影响  
  
## 评审建议  
  
根据以上检查，提供具体的改进建议：  
1. ** 必须修改 **：影响功能和安全的问题  
2. ** 建议改进 **：代码质量和性能优化  
3. ** 可选优化 **：最佳实践和代码风格  
  
## 测试验证  
  
** 自动化测试 **：  
- 单元测试：!`npm test -- --testPathPattern=$ARGUMENTS`  
- 集成测试：!`npm run test:integration`  
- 代码覆盖率：!`npm run test:coverage`  
  
** 手动测试要点 **：  
- 功能验证步骤  
- 边界条件测试  
- 异常场景验证  
  
请对 "$ARGUMENTS" 进行全面的代码评审。  
EOF
```

#### 上下文压缩命令

在大型项目和团队环境中，Claude Code 的使用效率直接影响开发体验。通过合理的优化策略和最佳实践，可以显著提升 AI 辅助开发的效果。

- 命令

```shell
cat > ~/.claude/commands/optimization/context.md << 'EOF'  
```

- md

```
---  
description: 智能上下文管理和优化  
---  
  
# 上下文优化：$ARGUMENTS  
  
## 上下文分析  
  
** 对话长度 **：当前对话包含约 X 条消息  
**Token 使用 **：预估 token 消耗情况  
** 关键信息 **：识别核心讨论内容  
  
## 压缩策略  
  
### 1. 保留关键信息  
- ** 项目背景 **：@CLAUDE.md  
- ** 当前任务 **：$ARGUMENTS  
- ** 重要决策 **：技术选型、架构设计  
- ** 待解决问题 **：阻塞问题、技术难点  
  
### 2. 移除冗余内容  
- 重复的代码片段  
- 已解决的问题讨论  
- 过程性的调试信息  
- 不相关的话题  
  
### 3. 结构化总结  
** 项目状态 **：  
- 当前进度  
- 完成的功能  
- 待开发特性  
  
** 技术栈 **：  
- 主要框架和库  
- 开发工具配置  
- 部署环境  
  
** 约定规范 **：  
- 代码风格  
- 命名约定  
- 文件组织  
  
## 优化建议  
  
基于分析结果，建议在以下时机进行上下文压缩：  
- 对话消息超过 50 条  
- 讨论主题发生转换  
- 开始新的开发阶段  
- Token 使用接近限制  
  
使用 `/compact` 命令执行压缩，保留核心上下文信息。  
EOF
```

#### 会话状态管理命令

- 命令

```shell
cat > .claude/commands/session/save-state.md <<EOF
```

- md

```
---
description: 保存和恢复会话状态
allowed-tools: Write, Read, Edit
---

# 会话状态管理：$ARGUMENTS

## 状态信息收集

### 当前工作内容
** 主要任务 **：$ARGUMENTS
** 进度状态 **：[进行中 / 已完成 / 待开始]
** 完成度 **：X%

### 技术上下文
** 项目信息 **：
- 项目类型：@package.json
- 主要依赖：当前使用的框架和库
- 开发环境：Node.js, Python, etc.

** 代码状态 **：
- 工作分支：!`git branch --show-current`
- 未提交更改：!`git status --porcelain`
- 最近提交：!`git log -3 --oneline`

### 决策记录
** 技术决策 **：
- 已选择的技术方案
- 架构设计决策
- 重要配置选择

** 待解决问题 **：
- 当前阻塞问题
- 需要讨论的技术点
- 优化改进计划

## 状态保存

将状态信息保存到 .claude/session-state.md：

"""
# 会话状态 - $(date +%Y-%m-%d)

## 当前任务
$ARGUMENTS

## 项目上下文
[项目基本信息]

## 工作进度
- [x] 已完成项目 1
- [ ] 进行中项目 2
- [ ] 待开始项目 3

## 技术决策
1. 选择 React + TypeScript 作为前端技术栈
2. 使用 Express.js 构建 API 服务
3. 采用 PostgreSQL 作为主数据库

## 待解决问题
1. 用户认证方案设计
2. 数据库性能优化
3. 前端状态管理选型

## 下次会话计划
1. 完成用户认证模块
2. 实现 API 接口
3. 编写单元测试
"""

## 恢复建议

下次会话开始时：

1. 阅读 `.claude/session-state.md` 了解上下文
2. 检查 git 状态确认代码变更
3. 继续未完成的任务
4. 更新会话状态
EOF
```
