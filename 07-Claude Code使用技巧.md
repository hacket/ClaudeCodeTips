---
banner: 
date_created: Sunday, July 20th 2025, 1:10:05 am
date_updated: Thursday, July 31st 2025, 11:31:11 pm
title: 07-Claude Code使用技巧
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
aliases: [Claude Code 使用技巧]
linter-yaml-title-alias: Claude Code 使用技巧
---

# Claude Code 使用技巧

## 网络

### 上下文

#### 用好 claude.md: 写出 " 零 Bug" 代码的关键

Claude Code 支持自定义规则文件 `claude.md`，这是许多用户忽视的神技，它是 Claude 的 " 工作手册 "，包含项目规则与流程规范。

你可以在项目目录中添加这个文件，写入一套 "`任务驱动规则`"，引导 Claude 以 "`先规划、再执行`" 的方式逐步生成代码。这套七条规则能让 Claude 把每一个请求都拆解成清晰可控的任务，还能自动生成任务目录，确保每一步有迹可循。

```
1. First think through the problem, read the codebase for relevant files, and write a plan to tasks/todo.md.  
2. The plan should have a list of todo items that you can check off as you complete them  
3. Before you begin working, check in with me and I will verify the plan.  
4. Then, begin working on the todo items, marking them as complete as you go.  
5. Please every step of the way just give me a high level explanation of what changes you made  
6. Make every task and code change you do as simple as possible. We want to avoid making any massive or complex changes. Every change should impact as little code as possible. Everything is about simplicity.  
7. Finally, add a review section to the todo.md file with a summary of the changes you made and any other relevant information.
```

立即复制这套规则并放入你的 `claude.md` 文件中，你会立刻感受到代码质量的巨大提升。

#### 注入额外上下文 ：执行任务一路开挂

##### 1、拖入代码库中的文件夹

不限于当前的代码库，也包括其他代码库中的其他文件夹。比如可以把后端目录一起拖进来，让它理解整体架构，甚至直接改后端。用这种方法也可以增加处理多个代码库的功能。

##### 2、通过 URL 访问文档

Claude Code 自带浏览器能力，可以粘贴文档链接，它会访问那个网站，阅读文档并获取它需要的任何上下文；或直接告诉它 " 用最新 Google Calendar API"，它会自行搜索并查阅文档获取 API 使用方式。

##### 3、发送图片处理

Claude Code 可以发送图片并进行处理，在命令行中，把图片和提示词发过去，让它更好的理解你的意图。

> 注意，在 Mac 中粘贴图片不是使用 command + v，而是使用 **ctrl + v** 快捷键。
> Windows 似乎不能 ctrl+v，只能拖进来

#### XML 标签结构化提示

Claude 对结构化语言比较敏感，使用类似 XML 的分块格式，可以显著提升提示词的清晰度与可控性。推荐使用如下结构：

```xml
<instruction>
你希望 Claude 执行的主要任务或目标
</instruction>

<context>
任务的背景信息，比如涉及的框架、业务逻辑、团队规范等
</context>

<code_example>
可以参考的代码片段、接口规范或已有实现
</code_example>
```

这种写法能帮助 Claude 更准确地区分 "**你要它做什么**" 和 "**你提供了哪些辅助信息**"，避免它把背景当成目标来执行。

#### prompt 技巧

- 把你的需求说具体点
- 把复杂的需求分步执行
- 先理解项目代码再开动
- 输错指令，按 ESC，随时打断它工作

### Claude Code 模式

#### 免授权模式 (Bypassing Permissions)

你是不是经常遇到 Claude Code 干活干一半，停下来让你授权？

不授权就卡在那里，虽然可以在当前会话可以设置不再询问，但权限又有好几种，每种权限都要来问一下，严重影响效率。

其实，启动 claude 时有一个参数：

```shell
claude --dangerously-skip-permissions
```

带上这个参数启动时，Claude Code 会出现警告提示：

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250722090949593.png)

你需要点确认（Yes）才能开启 **Bypassing Permissions** 模式，开启此模式后，终端下面会出现黄色的 `Bypassing Permissions` 模式提示：

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250722091044902.png)

开启 **Bypassing Permissions** 模式后，后面所有操作就都不需要你授权了，Claude Code 哐当就把所有活干了。

每次都输入 `claude --dangerously-skip-permissions` 太麻烦了，还可能输错，可以考虑他那一个 alias 别名：

```shell
alias claude='claude --dangerously-skip-permissions'
```

这只是临时生效，永久生效可以把它放到个人环境配置文件中，然后 source 生效一下。

把这个加入 `~/.claude/settings.json` 就不用每次手动输入 `claude --dangerously-skip-permissions` 了

```json
{
	"permissions": {
		"defaultMode": "bypassPermissions"
	}
}
```

##### 免授权模式，注意

要开启免授权模式，一般是需要配合 git 版本控制的；防止修改了没有 git 版本的文件，  

> 如：让 CC 检查下整个项目是否有密钥泄漏可能，CC 认为.env 里的密钥可能泄漏，就给改了，.env 配置在 gitignore 文件不会提交到 git，这样就找不回原来的密钥了

#### Plan 模式: Claude 真正发挥实力的关键 (plan mode)

Plan 模式是 Claude Code 最强大的特性之一，却经常被忽略或误用。  
只需按下 `Shift + Tab` 两次，即可进入 Plan 模式。在这里，你应该用自然语言清晰描述自己希望 Claude 执行的任务。Claude 会先帮你生成一个详细的执行计划，而不是直接生成代码。  
两个核心建议：

- 每一步都用 Plan 模式：别偷懒，每个微任务都应该规划。
- 搭配模型更高效：规划用 Opus，执行用 Sonnet，质量更高还省钱。

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250723000448980.png)

> 在项目前期需要规划功能的时候可以用到这个模式，它会自动给到计划方案，然后底部你是否执行：如点击第一个选项 `Yes, and auto-accept edits`，它就会进入「**自动接受编辑**」模式，根据 todo list 完成所有之前规划的功能。

按下 `shift + tab` 键，可以在**一般模式、auto-accept edits、plan mode**之间来回切换。

#### 自动接受编辑 ( auto-accept edits)

在 Claude Code 中，可以通过按下 `shift + tab` 键来切换到「**自动接受编辑**」功能（**auto-accept edits on**）:

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250723000522886.png)

开启此功能后，Claude Code 会自动创建、编辑文件，**而不是每次都需要我们手动确认**。

> 和 `--dangerously-skip-permissions` 模式相比，自动接受编辑功功能安全性要更高，首先它不是全局的，自动审批权限范围也仅限文件编辑。

### 基本

#### 用 GitHub 模拟 " 撤回 " 功能：Claude 没有回溯按钮怎么办

Claude Code 不像 Cursor 那样有 " 撤回到某步 " 的 `Checkpoint` 功能，因此你需要手动保存版本。

建议做法：每当 Claude 成功执行一个任务，立即 commit 到 GitHub；出错时，回退到上一个版本重新开始。这样能让你拥有几乎等效的 " 时间穿梭机 "。

#### 善用截图：灵感输入和 UI 调试的利器

Claude 支持图片输入功能，这让它在 UI 开发中如虎添翼。

- **场景一：界面灵感输入**  
用截图工具（如 Mac 上的 `Command + Shift + 4`）截取目标 UI，然后上传至 Claude，配合提示语 " 请参考这个截图构建界面 "，Claude 会精准复刻。

- **场景二：调试 Bug 或优化 UI**
截图错误页面或丑陋界面，交给 Claude，它能帮你自动修复。

#### 用好 /clear：节省成本、降低幻觉的法宝

长时间与 Claude 交互，会积累大量上下文信息，导致回复变慢、误差增多，甚至消耗大量 token。

建议：每完成一个阶段性任务，立即使用 `/clear` 命令，清除上下文。这不仅能让 Claude 表现更精准，还能显著节省月度额度。

#### 安全审查：AI 代码最大风险来自 " 安全盲区 "

大多数新手开发者会忽视安全问题，而 Claude 默认并不会主动防御所有安全漏洞。  
解决方法：每写完一段功能后，发送如下 Security prompt:

```
Please check through all the code you just wrote and make sure it follows security best practices. make sure there are no sensitive information in the front and and there are no vulnerabilities that can be exploited.
```

这是防止 `API 密钥暴露`、`XSS 漏洞` 等问题的有效手段，尤其适合准备上线的产品。  
也可以在任务结束后，要求它检查是否破坏其他功能、尝试找找边缘情况，确认是否一切正常。它总能找到我们忽略的东西，这可以让我们对它生成的代码多一点心安。

#### 向 Claude 学习：提升提示词水平的秘密武器

Claude 不只是工具，也是你的 AI 教练。  
每完成一个功能，不妨这样问 Claude：

```
Please explain the functionality and code you just built out in detail. Walk me through what you changed and how it works. Act like you’re a senior engineer teaching me code.
```

这样做能显著提升你对代码逻辑的理解，从而更精准地下达指令，最终获得更高质量的输出。

#### 运行子代理：效率倍增

Cloud Code 有能力启动子代理，在做非常庞大的任务时，可以让它拆分问题并同时跑多个 `sub-agent`。每个 sub-agent 拥有它们自己的上下文，会并行地执行任务，速度快非常多。

#### 等待 Claude 运行时保持专注的秘诀：用 Claude 当 " 陪伴教练 "

Claude 生成代码有时需要数分钟。在此期间，你是否会刷视频、走神、浪费时间？  
一个巧妙的方式可以帮你改变这个习惯：  
每当 Claude 正在执行任务，你就打开另一个 Claude 会话，与之探讨产品创意、复盘任务目标、制定下一步计划，进行头脑风暴。  
不仅避免了 " 精神内耗 "，还极大提升了效率和心态。

#### 激活 " 思考模式 "，攻克复杂问题

Claude Code 内置了一种特殊的深度推理模式，只需在指令中加入特定关键词，就能自动触发。

在 Claude Code 中，可以使用 "**think**" 这个词来激活深度思考模式，包括以下几种级别：

```shell
“think” < “think hard” < “think harder” < “ultrathink”
```

使用这些深度思考，会直接对应系统中不同级别的思考预算，每一级都会逐步增加 Claude 可用的思考预算，毫无疑问，使用 `ultrathink` 是最费钱的，也能发挥它的最大潜能。

如果你是订阅的是 Max 套餐，可以考虑使用 `ultrathink` 模式，不然就得小心你的钱包。

**示例：**

```shell
1+1=？ultrahink

# 一个 1+1 计算耗费了 0.06 美元，大概不到 5 毛 RMB。。
```

#### 使用 Git worktrees

在使用 Claude Code 时，如果你希望同时处理多个任务、多个分支，并让每个实例彼此完全隔离，Git worktrees 是非常实用的方案。

Git worktree 允许你将同一个 Git 仓库的不同分支分别检出到独立的目录中。每个 worktree 有自己的工作目录、隔离的文件状态，但共享相同的 Git 历史。

**1、创建新的 worktree**

```shell
# 如果你要创建新的分支并启动一个新的工作副本：
git worktree add ../project-feature-a -b feature-a

# 如果你已经有一个现成的分支：
git worktree add ../project-bugfix bugfix-123
```

这会在项目外部（如 `../project-feature-a`）创建一个独立的目录，其中包含该分支的完整工作目录。

**2、在每个 worktree 中运行 Claude Code**

```shell
# 进入新的 worktree：
cd ../project-feature-a
claude

# 再开一个 worktree：
cd ../project-bugfix
claude
```

每个会话运行在各自独立的代码环境中，Claude Code 实例互不干扰。

**3、管理 worktree**

```shell
# 列出当前所有 worktree：
git worktree list

# 删除一个 worktree：
git worktree remove ../project-feature-a
## 注意：删除 worktree 不会删除 Git 分支，只会清理目录。

```

**注意：**
- 每个 worktree 相当于独立的 Claude Code 沙盒，适合并行处理多个任务（如 bug 修复、特性开发、重构）。
- 在一个 worktree 中的更改不会影响另一个 worktree。
- 所有 worktrees 共享相同的 Git 历史和远程设置，便于统一推送。
- 使用有意义的目录名（如 `project-auth`, `project-api-v2`）能帮助你快速区分任务。
- 每个新 worktree 创建后，务必初始化开发环境，例如：
	- **JavaScript 项目**：`npm install`  
	- **Python 项目**：python -m venv .venv  
		source .venv/bin/activate  
		pip install -r requirements.txt

### 善用快捷键

- 输入 `/` 查看所有斜杠命令；
- 使用 `上下方向键` 查看命令历史；
- 使用 Tab 键进行命令快速补全；
- 使用 `Option + Enter` 换行；
- 使用 `Ctrl + C` 退出终端等等。

### Claude Code 的高效工作流

先用 Plan Mode 规划，再用 TDD 执行。

**具体操作流程** ⬇️  
1. 进入 Plan Mode，与 Claude 充分讨论设计架构、功能细节和边界条件，确认思路与测试方法。  
2. 明确计划后，退出 Plan Mode，开始执行。  
3. 使用 TDD 小步迭代：  
	- 先写测试，让测试失败（Red）；  
	- 再逐步实现功能，通过测试（Green）；  
	- 最后对代码重构优化（Refactor）。

**辅助技巧**
➡️ 在 prompt 里加入关键词 think through，激发 Claude 深度思考能力；  
➡️ 使用 /compact 定期清理上下文，提高 AI 稳定性；  
➡️ 在项目根目录创建 CLAUDE.md，定义项目规范和规则，减少误解需求的风险。

## 个人总结

### 实现 UI 效果

如果你有一张效果图，但是你不知道怎么描述，prompt 怎么写？你可以这样做：

- 将图片复制到 Claude Code (或其他支持读取图片的 LLM 模型中)，让大模型分析图片的内容，然后给出精确的 prompt，可以保存到 md 文件中去
- 将这个 md 和图片丢给 Claude Code 进行 Vibe Coding 了

### 生成模板代码命令

#### cart abt

```shell
# 使用
/project:/cart_abt


```

配合浏览网页的 mcp，给一个 poskey 的关键字，自动从网页爬取相应的字段填到

#### bean 生成

- 自定义一个 slash-command 命令
- 配合 `mcp-chrome` 自动生成

### 完成需求

1. 先用 `mcp-chrome` 和 `plan mode` 生成 md 需求单
2. 在 plan mode 下再次对齐校验需求单
3. 对齐好需求单后，再用 `auto-accept edits` 模型下编码
4. 再喂给 CC 实现需求
5. 收尾的，安全，代码指令的 review，规范是否符合等
