---
banner: 
date_created: Saturday, July 26th 2025, 8:33:33 pm
date_updated: Monday, August 4th 2025, 11:45:17 pm
title: Spec工作流
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
aliases: [Spec 开发工作流]
linter-yaml-title-alias: Spec 开发工作流
---

# Spec 开发工作流

## 什么是 Spec 开发工作流

一个 Spec 可以说是一个规格/规范，如果用过 BDD (行为驱动开发) 可能就会比较熟悉这个名词。

Spec 是用来解决如何把模糊的想法转化为详细的实施计划、跟踪和验收标准的问题。

每个 Spec 都是一个文件夹，下有 3 个核心文件：

1. `requirements.md` —— **需求文档**（用 `EARS` 语法写用户故事和验收标准）
2. `design.md` —— **系统设计**（架构、流程、注意事项）
3. `tasks.md` —— **任务清单**（todolist，便于跟踪）

## Spec 工作流的三阶段解析

### requirements.md 用 EARS 语法消除需求歧义

Kiro 引入 `EARS`（Easy Approach to Requirements Syntax）语法，以标准句式消除需求歧义。

EARS 语法通常包含 `事件驱动`、`状态驱动` 等句式，格式如下：

```
用户故事 (User Story): As a [role], I want [feature], so that [benefit].
验收标准 (Acceptance Criteria): WHEN [event] THEN the system SHALL [response].
```

明确需求的同时，自动生成对应测试用例和设计方案，从根本保障交付质量。

### design.md 从技术视角构建可行的系统蓝图

在需求明确后，Spec 工作流进入设计阶段。

design.md 是连接需求与实现的技术桥梁，其内容通常包括：

- 系统架构图 (Architecture)
- 组件与接口定义 (ComponentsandInterfaces)
- 数据模型 (DataModels)
- 错误处理机制 (ErrorHandling)
- 测试策略 (TestingStrategy)

一个高质量的设计文档，能让 AI 在生成代码时具备全局视野，确保各个模块之间的协同性和可维护性。Kiro 能够基于 requirements.md 自动生成设计草案，并通过与开发者的迭代沟通，最终形成一份生产级的技术设计文档。

### tasks.md：任务清单驱动的精细化执行

tasks.md 是将宏观设计分解为微观、可执行编码任务的清单。Kiro 强调任务的原子性和可执行性，每个任务都应是离散、可管理的编码步骤，并明确关联到 requirements.md 中的具体需求点。

任务清单通常采用带复选框的格式，支持多层级结构，确保开发过程的有序和可追溯。这种设计使得开发者可以精确控制 AI 的执行范围，一次只专注于一个任务，并通过任务状态管理实时追踪项目进度。

## 什么是 EARS 需求语法？

EARS（简易需求语法）最早用于喷气发动机控制系统，后来被软件工程广泛采用。它用简单句式约束需求，避免 " 模糊表达 "，让需求更清晰、可落地。

EARS 语法指南: <https://alistairmavin.com/ears/>

## Claude Code/Cursor 复刻这套 kiro Spec  

即使没有 Kiro，其他 AI IDE 也能复刻这套流程。以 Claude Code 为例，整个过程可以非常丝滑：

**在项目下建立 CLAUDE.md**
- 可以把下面的提示词模板直接写进 CLAUDE.md，作为 AI 协作的 " 工作说明书 "。
- 最新版本的提示词可以在这个 [Github链接](https://github.com/TencentCloudBase/CloudBase-AI-ToolKit/blob/main/config/.cursor/rules/cloudbase-rules.mdc#L21C1-L59C12)  获取。

**启动 Claude Code，输入原始需求**
- 直接把你的想法、用户故事写进对话框。
- Claude 会自动读取 CLAUDE.md，开始和你进行需求澄清和确认。

**需求确认后，Claude 输出 requirements.md**
- Claude 会用 EARS 语法梳理需求，生成标准的 requirements.md。
- 你可以随时补充、修改，Claude 会持续和你对齐。

**技术方案设计**
- 需求确认后，Claude 会自动进入 design.md 阶段，输出详细的技术方案。
- 包括架构、技术选型、接口、测试策略等。

**任务拆分**
- Claude 会根据 design.md 自动生成 tasks.md，把方案拆分为可执行的 todolist。

**逐步实现与验收**
- Claude 会按照 tasks.md 协助你逐步实现代码、测试，并输出所有过程产物到 output/ 目录。
- 你只需参与需求、设计、验收等关键评审环节。

整个流程下来，你会发现，AI 不再是 " 黑箱 " 式地帮你生成代码，而是和你像搭档一样，**步步确认、逐步推进**。

这样，哪怕没有 Kiro，借助 Claude Code 也能轻松复刻 Spec 工作流，让 AI 编程变得高效、可控、可复盘。

其他的 AI IDE 也是可以类似如此操作，例如 Cursor 的 `.cursor/rules/project.mdc`、Augment 的 `.augment-guidelines` 文件等。

#### Kiro AI System Prompt gist

<https://gist.github.com/CypherpunkSamurai/ad7be9c3ea07cf4fe55053323012ab4d>

#### AI 编程不靠运气，Kiro Spec 工作流复刻全攻略

<https://mp.weixin.qq.com/s/3j6lG50isbuSH4p64TsNag>

#### Vibe Coding 的终极工作流

经过数月的实践和调优，我将自己的 Claude Code 配置开源。这套配置不仅包含了常用的 Claude Code 配置和自定义命令，还参考 Kiro，引入了 **Vibe Coding** 流程，支持规范驱动的开发流程。

所有配置和自定义命令开源在 Github 上，项目地址为：<https://github.com/feiskyer/claude-code-settings>。

- [开源我的 Claude Code 配置：Vibe Coding 的终极工作流](https://mp.weixin.qq.com/s/QlqKEZoXJnxR1upn_U-wSg)

## Prompt Engineering 和 Context Engineering

- 3 分钟搞定！从 TRAE2.0 的 SOLO 模式看 Claude Code 上下文工程
<https://mp.weixin.qq.com/s/1BsDqXGbhsFAkWYA2SNbkA>

## spec 工作流实战

### 今天吃什么？

```shell
/kiro:/spec 给我生成一个今天吃什么的网站，用来解决每天下班后不知道吃什么，支持用户输入菜单或店铺名，用户点击一个按钮随机出现一个菜单或店铺，每人每餐只能随机1次
```

然后生成一个 `.kiro/spec/meal-randomizer/requirements.md` 文件：[[requirements]]

接着生成系统设计 `design.md`: [[design]]

然后给出计划 `tasks.md`: [[tasks]]
