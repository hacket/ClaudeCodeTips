---
banner: 
date_created: Thursday, July 31st 2025, 11:52:32 pm
date_updated: Saturday, August 2nd 2025, 11:24:47 pm
title: Claude Code Hooks
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
aliases: [Claude Code Hooks]
linter-yaml-title-alias: Claude Code Hooks
---

# Claude Code Hooks

## CC Hooks 介绍？

Claude Code Hooks 是用户定义的 shell 命令，在 Claude Code 生命周期的各个点执行。Hooks 提供对 Claude Code 行为的确定性控制，确保某些操作总是发生，而不是依赖 LLM 选择运行它们。

## CC Hooks 基础

### 配置

#### settings.json 配置文件

Claude Code 钩子在您的 [settings.json设置文件](https://docs.anthropic.com/zh-CN/docs/claude-code/settings) 中配置：

- `~/.claude/settings.json` - 用户设置
- `.claude/settings.json` - 项目设置
- `.claude/settings.local.json` - 本地项目设置（不提交）
- 企业管理策略设置

#### structure 结构

钩子按 `matchers` 组织，每个 `matchers` 可以有多个钩子：

```json
{
  "hooks": {
    "EventName": [
      {
        "matcher": "ToolPattern",
        "hooks": [
          {
            "type": "command",
            "command": "your-command-here"
          }
        ]
      }
    ]
  }
}
```

- **matcher**: 匹配工具名称的模式，区分大小写（仅适用于 `PreToolUse` 和 `PostToolUse`）
	- 简单字符串精确匹配：`Write` 仅匹配 Write 工具
	- 支持正则表达式：`Edit|Write` 或 `Notebook.*`
	- 使用 `*` 匹配所有工具。您也可以使用空字符串（`""`）或留空 `matcher`。
- **hooks**: 当模式匹配时要执行的命令数组
	- `type`: 目前仅支持 `"command"`
	- `command`: 要执行的 bash 命令（可以使用 `$CLAUDE_PROJECT_DIR` 环境变量）
	- `timeout`: （可选）命令应运行多长时间（以秒为单位），超时后 取消该特定命令。

对于像 `UserPromptSubmit`、`Notification`、`Stop` 和 `SubagentStop` 这样不使用 `matcher` 的事件，您可以省略 `matcher` 字段：

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/prompt-validator.py"
          }
        ]
      }
    ]
  }
}
```

#### Project-Specific Hook Scripts 项目特定 Hook 脚本

您可以使用环境变量 `CLAUDE_PROJECT_DIR`（仅在 Claude Code 生成钩子命令时可用）来引用存储在项目中的脚本， 确保无论 Claude 的当前目录如何，它们都能正常工作：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/check-style.sh"
          }
        ]
      }
    ]
  }
}
```

### Hook Events

#### PreToolUse function call 之前执行

在 Claude 创建 `tool parameters` 之后、处理 `tool call` 之前运行。

**常见 matchers：**
- `Task` - 子代理任务（参见 [子代理文档](https://docs.anthropic.com/zh-CN/docs/claude-code/sub-agents)）
- `Bash` - Shell 命令
- `Glob` - 文件模式匹配
- `Grep` - 内容搜索
- `Read` - 文件读取
- `Edit`、`MultiEdit` - 文件编辑
- `Write` - 文件写入
- `WebFetch`、`WebSearch` - Web 操作

**PreToolUse 示例：**

```json
"PreToolUse": [
  {
    "matcher": "Read",
    "hooks": [
      {
        "type": "command",
        "command": "node /home/hooks/read_hook.ts"
      }
    ]
  }
]
```

在这个例子中：

- 每當 Claude 嘗試使用 Read 工具時，就會先執行指定的指令 `node read_hook. ts`
- 你的命令會從標準輸入（stdin）接收到工具調用的 JSON 詳細資料
- 然後你可以根據這些資訊做出兩種決策：
	- 允許：回傳 exit code 0，Claude 將正常繼續執行工具
	- 阻擋：回傳 exit code 2，Claude 將中止該操作，並顯示 stderr 作為錯誤提示

#### PostToolUse function call 之後執行

在工具成功完成后立即运行。识别与 `PreToolUse` 相同的 matchers 值。

是在 Claude 执行工具之后调用，因为无法阻止操作，但你可以进行：

- 文档格式化
- 对 Claude 回传附加信息或后处理结果

```json
"PostToolUse": [
  {
    "matcher": "Write|Edit|MultiEdit",
    "hooks": [
      {
        "type": "command", 
        "command": "node /home/hooks/edit_hook.ts"
      }
    ]
  }
]
```

在这个例子中：

- 当 Claude 执行写入或编辑后，就会自动执行 `edit_hook.ts`
- 这个脚本可以读取 Claude 刚刚操作的内容，例如格式、文件名、变更差异等，并进行额外后处理

#### Notification

#### UserPromptSubmit

#### Stop

#### SubagentStop

#### PreCompact

#### SessionStart

## CC Hooks 坑？

- 默认不是输出到终端，需要按 Ctrl+R
- 输入是标准流输入

## CC Hooks 使用场景

### 修改后代码格式化

- CC 修改完代码后，格式化代码

### 自动化测试

代码变更后触发单元测试或整体测试

### 存取控制

阻止 Claude Code 读取或编辑敏感文档。

假設使用者輸入 "read a.env file"，但我們不想要 Claude 去讀取。

### 程序质量检查

执行 linter 或 type checker，并将错误反馈给 Claude Code

- 任务完成后，发消息

## 开源

### awesome-claude-code#hooks

<https://github.com/hesreallyhim/awesome-claude-code?tab=readme-ov-file#hooks>

## Ref

- Claude Code hooks <https://docs.anthropic.com/en/docs/claude-code/hooks-guide>
- Hooks reference <https://docs.anthropic.com/en/docs/claude-code/hooks>
