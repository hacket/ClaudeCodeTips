---
banner: 
date_created: Saturday, July 19th 2025, 12:09:46 am
date_updated: Tuesday, August 5th 2025, 12:02:17 am
title: 05-Claude Code进阶用法
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
aliases: [Claude Code 进阶用法]
linter-yaml-title-alias: Claude Code 进阶用法
---

# Claude Code 进阶用法

## thinking mode

### thinking mode 介绍

Claude Code 怎么控制思考时间长短的吗？在提示词里面说："ultrathink" 或者 "think harder" 或者 " 仔细思考 "，那么它就会真的多思考一会。

不是玄学，而是写在代码里面的，Claude Code 把思考级别分成了 4 档：

- `HIGHEST`： 32K
- `MIDDLE`：10K
- `BASIC`：4K
- `NONE`： 0

举例来说，如果你说了 "`ultrathink`"，那么它思考的 Token 最长可以到 32K Tokens，如果只说关键字 "think" 只有 4K，到了 4K 就结束了。如果这些关键字都没有，那么就不会调用 Think Tool（思考工具），直接开始任务。

示例：

```shell
I need to implement a new authentication system using OAuth2 for our API. Think deeply about the best approach for implementing this in our codebase. 

think about potential security vulnerabilities in this approach 

think harder about edge cases we should handle 
```

### 各语言触发 thinking mode 关键词

```
English (英语)
- HIGHEST: "think harder", "think intensely", "think longer", "think really hard", "think super hard", "think very hard", "ultrathink"
- MIDDLE: "think about it", "think a lot", "think deeply", "think hard", "think more", "megathink"
- BASIC: "think"

Chinese (中文)
- HIGHEST: "多想一会", "深思", "仔细思考"
- MIDDLE: "多想想", "好好想"
- BASIC: "想", "思考"
- NONE: (无关键词)

Japanese (日语)
- HIGHEST: "熟考", "深く考えて", "しっかり考えて"
- MIDDLE: "もっと考えて", "たくさん考えて", "よく考えて", "長考"
- BASIC: "考えて"

Korean (韩语)
- HIGHEST: "더 오래 생각", "깊이 생각", "심사숙고", "곰곰이 생각"
- MIDDLE: "많이 생각", "더 생각", "잘 생각"
- BASIC: "생각"

Spanish (西班牙语)
- HIGHEST: "piensa más", "piensa mucho", "piensa profundamente"
- MIDDLE: "piensa"
- BASIC: "pienso", "pensando"

French (法语)
- HIGHEST: "réfléchis plus", "réfléchis beaucoup", "réfléchis profondément"
- MIDDLE: "réfléchis"
- BASIC: "pense", "réfléchir"

German (德语)
- HIGHEST: "denk mehr", "denk gründlich", "denk tief"
- MIDDLE: "denk nach", "denk"
- BASIC: "denke", "nachdenken"

Italian (意大利语)
- HIGHEST: "pensa di più", "pensa a lungo", "pensa profondamente", "rifletti a fondo"
- MIDDLE: "pensa", "pensa molto", "rifletti"
- BASIC: "penso", "pensare", "pensando", "riflettere"
```

## Hooks 钩子

<https://docs.anthropic.com/zh-CN/docs/claude-code/hooks>

钩子是 Claude Code 的一个强大功能，允许你在 `特定事件`（如任务完成、文件修改）时自动触发自定义操作。例如：

- `Stop Hook`：任务完成后播放系统提示音，提醒你 AI 已完成工作
- `Post-Tool Hook`：在 AI 修改或创建文件后，运行一个 Python 脚本进行类型检查，类似 Cursor 的错误检测功能。如果发现类型错误，脚本会将错误信息反馈给 Claude Code，让它主动修复

一个复杂示例：通过钩子检查 TS 文件（`.ts` 或 `.tsx`）的类型错误，并阻止 AI 继续执行，直到问题解决。这种自动化机制大大减少了手动调试的时间

见 [[Claude Code Hooks]]

## Sub Agents

见 [[Claude Code Sub Agents]]

## Claude Code GitHub Action

Claude Code GitHub Actions 是一套 AI 驱动的 GitHub 自动化工具，旨在将 Claude 的代码生成与协作能力无缝整合进你的开发工作流。

**安装方式**：在 Claude Code 中执行 `/install-github-app` ，按照流程安装即可。

安装完成后，你可以在 PR 或者 issue 中 `@claude`，让它完成指定任务：

**通过 issue 自动生成 PR：**

```shell
@claude implement this feature based on the issue description
```

**请求代码建议：**

```shell
@claude how should I implement user authentication for this endpoint?
```

**修复 bug：**

```shell
@claude fix the TypeError in the user dashboard component
```

## Claude Code Proxy 让 Claude 连接其他大模型

## Claude Code router
