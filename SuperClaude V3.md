---
banner: 
date_created: Friday, July 25th 2025, 12:07:08 am
date_updated: Friday, July 25th 2025, 11:24:41 pm
title: SuperClaude V3
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
aliases: [SuperClaude V3]
linter-yaml-title-alias: SuperClaude V3
---

# SuperClaude V3

<https://github.com/SuperClaude-Org/SuperClaude_Framework>

使用文档：

<https://github.com/SuperClaude-Org/SuperClaude_Framework/blob/master/Docs/superclaude-user-guide.md>

## SuperClaude 介绍

SuperClaude 是基于 Claude Code 的扩展，它是一个能通过**专用命令、角色设定、开发方法和 MCP 服务器**集成来增强 Claude Code 功能的框架，**想当于 MyBatis-Plus 和 MyBatis 的关系**。

核心亮点  
SuperClaude v 3 就像给 Claude Code 装了个 " 强化插件 "，主要提供以下几个牛掰的功能：

一、16 个开发专用命令这些命令覆盖了开发中的常见任务，比如：  

- 开发相关：/sc:implement（实现功能）、/sc:build（编译打包）、/sc:design（设计方案）  
- 分析相关：/sc:analyze（代码分析）、/sc:troubleshoot（排查问题）、/sc:explain（解释代码）  
- 质量相关：/sc:improve（优化代码）、/sc:test（测试）、/sc:cleanup（清理代码）  
- 还有一些辅助命令，比如 /sc:document（写文档）、/sc:git（Git 操作）等  
这些命令让 Claude 能更精准地处理开发任务，而不是泛泛而谈。

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250723004518638.png)

二、智能角色（Personas）  
SuperClaude 内置了多个 "AI 专家 "，会根据你的任务自动切换合适的角色：  

- 架构师：帮你设计系统结构  
- 前端专家：搞定 UI/UX 和可访问性  
- 后端专家：处理 API 和基础设施  
- 安全专家：关注代码安全问题  
- 还有文档专家、分析专家等，总共 11 个角色  
这些角色会尽量根据你的需求 " 上场 "，虽然有时候可能选得不完美，但大部分时候挺靠谱。

三、MCP 工具整合  
项目接入 MCP 服务器，增强 Claude 的能力：  

- Context 7：自动拉取官方文档和设计模式  
- Sequential：支持复杂的多步推理  
- Magic：生成现代化的 UI 组件  
- Playwright：用于浏览器自动化和测试  

四、任务管理和优化  

- 内置任务管理功能，帮你跟踪项目进度  
- 优化了 Claude 的 token 使用，能支持更长的对话，减少 " 内存 " 不足的问题

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250723003752227.png)

## 安装

Mac/Linux/WSL:

```shell
# 安装python3
brew install python3
# 安装uv
curl -Ls https://astral.sh/uv/install.sh | sh
# 建立虚拟环境并启动
uv venv
source .venv/bin/activate

# 安装SuperClaude
uvx pip install SuperClaude
## 或者：如果你偏好跨平台工具，也可使用 uvx：
uvx pip install SuperClaude

# 框架安装
python3 -m SuperClaude install
## 或者
SuperClaude install
```

安装完成后也可以通过修改以下文档来定制 SuperClaude：

- `~/.claude/settings.json` - 主要配置文件；
- `~/.claude/*.md` - 框架行为文件；

使用方法很简单，进入 Claude 命令行后，输入 `/sc:` 会自动弹出相关的增强命令；如以下基本用法：

```shell
/sc:design user-auth-system # 设计一套用户认证系统  
/sc:design --type api auth # 专门设计 API 部分  
/sc:design --format spec payment # 创建正式规范  
  
/sc:git commit # 智能提交，消息自动生成  
/sc:git --smart-commit add . # 添加并提交，带智能消息  
/sc:git branch feature/new-auth # 创建并切换到新分支
```

## SuperClaude 原理

SuperClaude 通过以下方式增强的 Claude Code：

1. **框架文件**：文档安装在 `~/.claude/` 目录下，通过这些文档指导 Claude 如何工作；
2. **自定义命令**：定制了 17 个用于不同开发任务的命令；
3. **MCP 服务器**：添加额外功能的外部服务；
4. **智能路由**：根据你的工作自动选择合适的工具和专家；
SuperClaude 官方描述，大多数时候，它可以与 Claude Code 的现有内容完美兼容。

## SuperClaude 扩展能力

###  **17 Specialized Commands** 专业命令

<https://github.com/SuperClaude-Org/SuperClaude_Framework/blob/master/Docs/commands-guide.md>

SuperClaude 的所有功能都通过命令调用。基本语法如下：

```shell
/command [flags] [arguments]
```

#### 命令汇总

| 命令                 | 目的        | 自动激活      | 最适合的人群            |
| ------------------ | --------- | --------- | ----------------- |
| `/sc:analyze`      | 智能代码分析    | 安全/性能专家   | 发现问题，理解代码库        |
| `/sc:build`        | 智能构建      | 前端/后端专家   | 编译、打包、部署准备        |
| `/sc:implement`    | 功能实现      | 领域专家      | 创建功能、组件、API、服务    |
| `/sc:improve`      | 自动清理代码    | 质量专家      | 重构、优化、质量修复        |
| `/sc:troubleshoot` | 问题排查      | 调试专家      | 调试、问题分析           |
| `/sc:test`         | 智能测试      | 测试专家      | 运行测试、覆盖率分析        |
| `/sc:document`     | 自动文档生成    | 文档专家      | 编写 README、代码注释、指南 |
| `/sc:git`          | 增强 Git 流程 | DevOps 专家 | 智能提交、分支管理         |
| `/sc:design`       | 系统设计协助    | 架构专家      | 架构规划、API 设计       |
| `/sc:explain`      | 学习助手      | 教学专家      | 学习概念、理解代码         |
| `/sc:cleanup`      | 技术债清理     | 重构专家      | 删除冗余代码、整理文件       |
| `/sc:load`         | 上下文理解     | 分析专家      | 项目分析、理解代码库        |
| `/sc:estimate`     | 智能预估      | 规划专家      | 时间/工作量评估、复杂度分析    |
| `/sc:spawn`        | 复杂流程调度    | 编排系统      | 多步骤操作、自动化流程       |
| `/sc:task`         | 项目管理      | 规划系统      | 长期功能规划、任务追踪       |
| `/sc:workflow`     | 实现规划      | 工作流系统     | 从 PRD 创建逐步实施流程    |
| `/sc:index`        | 命令导航      | 帮助系统      | 找到适合你任务的命令        |

#### 简单使用

```shell
/sc:analyze README.md       # SuperClaude analyzes your project
/sc:workflow feature-prd.md # Generate implementation workflow from PRD (NEW!)
/sc:implement user-auth     # Create features and components (NEW in v3!)
/sc:build                   # Smart build with auto-optimization  
/sc:improve messy-file.js   # Clean up code automatically
```

#### Planning tools

- `/workflow` (NEW!)
command for `PRD-to-implementation planning`
- `/estimate`
- `/task`

##### /workflow 生成工作流

**能做什么：**
分析 `PRDs` 和 `feature requirements` 创建一步步综合的工作流。

**有用的部分：**
将你的 PRD 分解为结构化的实施计划，并提供 `expert guidance`、`dependency mapping` 和 `task orchestration`！

**什么时候用：**
- 从 `PRD` 或 `specification` 开始新功能
- 需要明确的实施路线图
- 需要 `expert` 指导实施策略
- 规划具有多个依赖关系的复杂功能

**魔法：**
根据你的功能需求自动激活适当的 expert personas（architect 架构师、security 安全、frontend 前端、backend 后端）和 MCP 服务器（用于模式的 Context7、用于复杂分析的 Sequential）。

**示例：**

```shell
/sc:workflow docs/feature-100-prd.md --strategy systematic --c7 --sequential
/sc:workflow "user authentication system" --persona security --output detailed
/sc:workflow payment-api --strategy mvp --risks --dependencies
```

**你能得到什么：**
- **Roadmap Format**: Phase-based implementation plan with timelines
- **Tasks Format**: Organized epics, stories, and actionable tasks
- **Detailed Format**: Step-by-step instructions with time estimates
- **Risk Assessment**: Potential issues and mitigation strategies
- **Dependency Mapping**: Internal and external dependencies
- **Expert Guidance**: Domain-specific best practices and patterns

#### Development tools

- `/implement` command for feature creation (restores v2 functionality)
- `/build`
- `/design`

#### Analysis tools

- `/analyze`
- `/troubleshoot`
- `/explain`

#### Quality tools

- `/improve`
- `/cleanup`
- `/test`

### **11 Smart Personas** 智能角色，知道何时介入 角色即标志 (Personas as Flags)

<https://github.com/SuperClaude-Org/SuperClaude_Framework/blob/master/Docs/personas-guide.md>

SuperClaude 可以为不同领域挑选合适的专家：

- 🏗️ **架构师（architect）** - 系统设计和架构；
- 🎨 **前端（frontend）** - 用户体验和 React 开发；
- ⚙️ **后端（backend）** - API 和基础设施；
- 🔍 **分析器（analyzer）** - 调试并解决问题；
- 🛡️ **安全（security）** - 安全问题和漏洞；
- ✍️ **scribe** - 文档和写作；
- **mentor**：教学和指导
- **refactorer**：代码质量和简化
- **performance**：性能优化
- **qa**：质量保证和测试

当 AI 大模型认为某个角色相关时，它们会自动介入相对应的角色，**一个人相当于一个团队**。

为命令注入特定的专业视角和思维模式。

| 角色标志                    | 专业领域            | 最适用场景      |
| ----------------------- | --------------- | ---------- |
| `--persona-architect`   | 系统思维、可扩展性、设计模式  | 架构决策、系统设计  |
| `--persona-frontend`    | 痴迷 UI/UX、可访问性优先 | 用户界面、组件设计  |
| `--persona-backend`     | API、数据库、可靠性     | 服务器架构、数据建模 |
| `--persona-analyzer`    | 根本原因分析、基于证据     | 复杂调试、问题调查  |
| `--persona-security`    | 威胁建模、零信任、OWASP  | 安全审计、漏洞评估  |
| `--persona-mentor`      | 教学、引导式学习、清晰表达   | 文档、知识传递    |
| `--persona-refactorer`  | 代码质量、可维护性       | 代码清理、技术债务  |
| `--persona-performance` | 优化、性能分析、效率      | 性能调优、瓶颈分析  |
| `--persona-qa`          | 测试、边缘案例、验证      | 质量保证、测试覆盖  |

### **MCP Server Integration**

MCP (Master Control Program) 是 SuperClaude 的大脑，提供了一系列强大的后端服务来增强命令的能力。通过通用标志可以精细地控制这些服务的启用与禁用。

- **Context7 (`--c7`)**: 在执行任务时，能够实时查询最新的外部文档和知识库，确保生成的代码或建议基于当前最佳实践。
- **Sequential Thinking (`--seq`)**: 模拟人类专家的思考过程，将复杂问题分解为一系列逻辑步骤，并展示其推理过程。非常适合用于架构设计、根本原因分析等场景。
- **Magic UI (`--magic`)**: 在构建前端项目时，可以根据需求自动生成高质量的 UI 组件代码。
- **Playwright**: 提供浏览器自动化能力，可用于端到端测试、性能分析、网页抓取等任务。
- `Puppeteer`(`--pup`)

组合使用这些服务可以发挥出 SuperClaude 的最大潜力。例如，在构建一个需要调用外部库 API 的前端功能时，可以同时启用 `--react`、`--magic` 和 `--c7`。

### 通用标志 (Universal Flags)

这些标志可以附加到任何 SuperClaude 命令上，以增强或修改其行为。

#### 思维深度控制

| 标志             | 描述                    | 大约 Token 用量 |
| -------------- | --------------------- | ----------- |
| `--think`      | 标准分析模式：使用扩展上下文进行多文件分析 | ~4K tokens  |
| `--think-hard` | 深度分析模式：进行架构级别的深度分析    | ~10K tokens |
| `--ultrathink` | 关键分析模式：以最大深度进行关键系统分析  | ~32K tokens |

#### 令牌优化

| 标志     | 别名                  | 描述                             |
| ------ | ------------------- | ------------------------------ |
| `--uc` | `--ultracompressed` | 激活 UltraCompressed 模式以大幅减少令牌消耗 |

#### MCP 服务控制

| 标志           | 描述                    |
| ------------ | --------------------- |
| `--c7`       | 启用 Context7 进行文档查找    |
| `--seq`      | 启用 Sequential 深度思维      |
| `--magic`    | 启用 Magic UI 组件生成      |
| `--pup`      | 启用 Puppeteer 浏览器自动化   |
| `--all-mcp`  | 启用所有 MCP 服务以获得最大能力    |
| `--no-mcp`   | 禁用所有 MCP 服务 (仅使用原生工具) |
| `--no-c7`    | 禁用 Context7           |
| `--no-seq`   | 禁用序列化思考               |
| `--no-magic` | 禁用 Magic UI 构建器       |
| `--no-pup`   | 禁用 Puppeteer          |

#### 计划与执行

| 标志              | 描述             |
| --------------- | -------------- |
| `--plan`        | 在运行前显示详细的执行计划  |
| `--dry-run`     | 预览变更而不实际执行     |
| `--watch`       | 持续监控并提供实时反馈    |
| `--interactive` | 进入分步指导的交互模式    |
| `--force`       | 覆盖安全检查 (请谨慎使用) |

#### 质量与验证

| 标志           | 描述             |
| ------------ | -------------- |
| `--validate` | 增强的执行前安全检查     |
| `--security` | 进行以安全为中心的分析和验证 |
| `--coverage` | 生成全面的覆盖率分析     |
| `--strict`   | 启用零容忍模式和增强验证   |

#### 特定功能标志

- `-init` - 项目初始化
- `-feature` - 功能开发
- `-tdd` - 测试驱动开发
- `-coverage` - 代码覆盖率
- `-e2e` - 端到端测试
- `-dry-run` - 预演模式
- `-rollback` - 回滚准备

### **Enhanced Task Management**

- /task
- /spawn
- /loop

### 示例

#### 复杂工作流示例: 完整开发流程

```shell
# 1. 项目规划
/design --api --ddd --plan --persona-architect

# 2. 前端开发
/build --react --magic --tdd --persona-frontend

# 3. 后端开发
/build --api --tdd --coverage --persona-backend

# 4. 质量检查
/review --quality --evidence --persona-qa

# 5. 安全扫描
/scan --security --owasp --persona-security

# 6. 性能优化
/improve --performance --iterate --persona-performance

# 7. 部署准备
/deploy --env staging --plan --persona-architect


```

#### 问题排查流程

```shell
# 1. 问题分析
/troubleshoot --investigate --prod --persona-analyzer

# 2. 根因分析
/troubleshoot --prod --five-whys --seq --persona-analyzer

# 3. 性能分析
/analyze --profile --perf --seq --persona-performance

# 4. 修复实施
/improve --quality --threshold 95% --persona-refactorer
```

#### Mac 右键 apk 的 app

```shell
/sc:workflow Mac右键apk的app
```

## Ref

- [我的 Claude Code 助手 - SuperClaude](https://mp.weixin.qq.com/s/ceTgiWuF2hfzCmimO3beog)
