---
banner: 
date_created: Wednesday, July 9th 2025, 11:17:37 pm
date_updated: Tuesday, August 5th 2025, 12:03:28 am
title: 01-Claude Code入门
author: hacket
categories:
  - AI
category: ClaudeCode
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
aliases: [Claude Code 入门]
linter-yaml-title-alias: Claude Code 入门
---

# Claude Code 入门

- Claude Code 官方中文文档：
<https://docs.anthropic.com/zh-CN/docs/claude-code/overview>

- Claude Code
<https://github.com/anthropics/claude-code>

- Anthropic courses
<https://anthropic.skilljar.com/>

## Claude Code 介绍

`Claude Code` 是 Anthropic 官方推出的终端 AI 编程助手，可以直接在命令行中与 Claude 对话，进行代码编写、项目管理、Git 操作等。

## 为什么选择 Claude Code？

1. 简单易用：Claude Code 是 CLI 形式，可以是 VS Code、Cursor 等 IDE 的扩展，启动方便，可同时在不同代码库部分运行多个实例
2. 更智能的编码体验：相比 Cursor，Claude Code 在处理复杂任务（如更新 1.8 万行的 React 组件）时表现更稳定，几乎不卡顿，且能更好理解大型代码库的结构和关系
3. 直接来自 Anthropic：Claude Code 由 Anthropic 开发，模型优化更专注，性价比高。这就像 " 直接从制造商购买 "，比通过第三方（如 Cursor）更划算

## Claude Code 安装

### Quick Installment

```shell
# Quick Installment

## Method 1 – NPM (global) ⭐️ Official
npm install -g @anthropic-ai/claude-code
# Requires Node 18+ on macOS / Linux / WSL  

## Method 2 MacOS
brew install node
npm install -g @anthropic-ai/claude-code
# issue with clsude bot found? run export PATH="$PATH:$(npm bin -g)"

## Method 3 – Arch Linux AUR
yay -S claude-code        # or paru -S claude-code
# Keeps pace with npm releases

## Method 4 – Docker (containerised)
mkdir -p ~/.docker/cli-plugins
curl -SL https://github.com/docker/buildx/releases/download/v0.11.2/buildx-v0.11.2.linux-amd64 -o ~/.docker/cli-plugins/docker-buildx
chmod +x ~/.docker/cli-plugins/docker-buildx
curl -O https://raw.githubusercontent.com/RchGrav/claudebox/main/claudebox
chmod +x claudebox
# Nice when you can't touch the host system

## Method 5 – Windows via WSL (Anthropic-recommended path)
# 1) Enable WSL 2 and install Ubuntu
# 2) Inside Ubuntu:
sudo apt update && sudo apt install -y nodejs npm
npm install -g @anthropic-ai/claude-code
# Step-by-step guide 

# You can also open claude code with ur entire project in vscode or cursor easy with:
cursor .
# or
code .
# cd in the directory u want to work with and run the command above!
# remeber to have the claude code extention installed

## Checn if claude is installed
which claude
claude --version

# Test installation
claude "Hello, Claude!"
claude /doctor

# Interactive Mode
claude                      # Start interactive REPL
claude "your question"      # Start with initial prompt

# One-Shot Mode  
claude -p "analyze this"    # Quick query and exit
cat file | claude -p "fix"  # Process piped content

# Management
claude config              # Configure settings
claude update              # Update to latest
claude mcp                 # Setup MCP servers
```

### Mac/Linux

Mac 和 Linux 比较简单

```shell
npm install -g @anthropic-ai/claude-code

claude
```

### Windows

**Claude Code 目前不支持直接在 Windows 上运行**，必须通过 WSL（Windows Subsystem for Linux）。
- ✅ 需要 Windows 10/11
- ✅ 需要 WSL 2
- ✅ 需要 Claude Pro 账号或 API key
- ✅ 网络环境（可能需要代理）

**安装 Warp Terminal 可直接使用 Claude Code，不用 WSL**

#### 安装和配置 WSL

下面的都要用 PowerShell，用 CMD 不行。更多见：[[WSL]]

- 首先检查 WSL 状态：

```powershell
wsl --list --verbose
```

- 如果没有安装 Linux 发行版，安装 Ubuntu：

```powershell
wsl --install -d Ubuntu
```

- 设置 Ubuntu 为默认：

```powershell
wsl --set-default Ubuntu
```

- 更新 WSL 目录，默认在 C 盘

**如果你使用的 IDE 编程工具是 VSCode、Cursor 之类的，可以使用 VSCode 的 WSL 插件，链接并使用你的 WSL。**

#### 解决网络问题

> 重要：claude 封号严重，一定要干净的节点

**问题现象**：WSL 中无法正常访问网络，提示 "NAT 模式下的 WSL 不支持 localhost 代理 "
**解决方案**：启用 WSL 镜像模式

- 退出所有 WSL 实例：

```powershell
wsl --shutdown
```

- 创建 WSL 配置文件：

```powershell
# 在Windows用户目录下创建 .wslconfig 文件
New-Item -Path "$env:USERPROFILE\.wslconfig" -ItemType File -Force
```

- 编辑配置文件 `.wslconfig` 内容：

```
[wsl2]
networkingMode=mirrored
```

- 重启 WSL

```powershell
wsl --shutdown
wsl
```

#### 代理配置（如果需要）

```shell
# 在WSL中设置代理，使用Windows主机IP
export http_proxy=http://192.168.1.20:33210
export https_proxy=http://192.168.1.20:33210

# PowerShell
$Env:http_proxy="http://127.0.0.1:7890";$Env:https_proxy="http://127.0.0.1:7890"
# CMD
set http_proxy=http://127.0.0.1:7890 & set https_proxy=http://127.0.0.1:7890
```

#### 安装 Node.js 环境

- 使用 NVM 安装 Node.js（推荐方式）：

```powershell
wsl 

# 安装NVM
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
 
# 重新加载配置
source ~/.bashrc
 
# 安装Node.js LTS版本
nvm install --lts
nvm use --lts
```

- 验证安装：

```powershell
node --version  # 应该显示 v22.x.x
npm --version   # 应该显示 10.x.x
```

#### 安装 Claude Code

```powershell
# 全局安装Claude Code
npm install -g @anthropic-ai/claude-code
 
# 验证安装
claude --version
```

#### Windows 中使用 Claude Code

```shell
wsl

claude
```

**一键启动 WSL 脚本：**

```bat
@echo off
chcp 65001
REM 设置窗口标题
title WSL Toggle for Ubuntu

REM --- 请在这里修改你的WSL发行版名称 ---
set DISTRO_NAME=Ubuntu
REM -----------------------------------------

echo Checking status of %DISTRO_NAME%...
echo.

REM 使用 wsl -l -v 命令检查状态，并通过 findstr 过滤出目标发行版和 "Running" 状态
REM findstr 命令如果找到匹配项，error level 为 0，否则为 1。
wsl -l -v | findstr "%DISTRO_NAME%" | findstr "Running" > nul

REM 判断上一个命令的执行结果
if %errorlevel% == 0 (
    REM 如果 errorlevel 是 0, 说明找到了 "Running" 状态，WSL 正在运行
    echo %DISTRO_NAME% is currently running.
    echo Shutting it down now...
    wsl --terminate %DISTRO_NAME%
    echo.
    echo %DISTRO_NAME% has been stopped.
) else (
    REM 如果 errorlevel 不是 0, 说明 WSL 已停止
    echo %DISTRO_NAME% is currently stopped.
    echo Starting it up...
    REM 使用 start 命令可以在一个新窗口中启动 WSL，这样脚本本身就可以结束
    start wsl.exe -d %DISTRO_NAME%
    echo.
    echo A new window for %DISTRO_NAME% should be opening.
)

echo.
echo Operation complete.
pause
```

#### WSL 安装故障排除

##### claude: command not found

<https://docs.anthropic.com/zh-CN/docs/claude-code/setup#wsl-%E5%AE%89%E8%A3%85%E6%95%85%E9%9A%9C%E6%8E%92%E9%99%A4>

- **操作系统/平台检测问题**: 如果您在安装过程中收到错误，WSL 可能正在使用 Windows `npm`。尝试：
	- 在安装前运行 `npm config set os linux`
	- 使用 `npm install -g @anthropic-ai/claude-code --force --no-os-check` 安装（不要使用 `sudo`）
- **找不到 Node 错误**: 如果您在运行 `claude` 时看到 `exec: node: not found`，您的 WSL 环境可能正在使用 Windows 安装的 Node.js。您可以通过 `which npm` 和 `which node` 确认这一点，它们应该指向以 `/usr/` 开头的 Linux 路径，而不是 `/mnt/c/`。要解决这个问题，请尝试通过您的 Linux 发行版的包管理器或通过 [`nvm`](https://github.com/nvm-sh/nvm) 安装 Node。

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250719092752430.png)

这种问题一般是用到了 Windows 的 npm

**方式 1：** 未安装 nvm 和 claude code

```shell
wsl

which npm 
# /mnt/c/Software/Node//npm，目前是Windows上的npm

# 安装nvm，见上面步骤
# 查看npm是哪个环境的
which npm
> /home/hacket/.nvm/versions/node/v22.17.1/bin/npm
# 安装claude code
npm install -g @anthropic-ai/claude-code
```

**方式 2：** 已经安装了 nvm 和 claude code，显示的是 Windows 上的 claude code，直接 source 下

```shell
wsl 
which npm 
source ~/.bashrc
```

![image.png](https://raw.githubusercontent.com/hacket/ObsidianOSS/master/obsidian/20250719093623643.png)

可以看到，如果可以用了，颜色都变了

## Claude Code 认证

1. **Anthropic Console**: 默认选项。通过 Anthropic Console 连接并 完成 OAuth 流程。需要在 [console.anthropic.com](https://console.anthropic.com/) 激活计费。
2. **Claude App（Pro 或 Max 计划）**: 订阅 Claude 的 [Pro 或 Max 计划](https://www.anthropic.com/pricing)，获得包含 Claude Code 和网页界面的统一订阅。以相同价格获得更多价值，同时在一个地方管理您的账户。使用您的 Claude.ai 账户登录。在启动期间，选择与您的订阅类型匹配的选项。
3. **企业平台**: 配置 Claude Code 使用 [Amazon Bedrock 或 Google Vertex AI](https://docs.anthropic.com/zh-CN/docs/claude-code/bedrock-vertex-proxies) 进行企业部署，使用您现有的云基础设施。

### Anthropic Console/Claude App

见官网，一步步来

### 企业认证

```shell
# 公司的认证
export ANTHROPIC_AUTH_TOKEN=sk-xxx
export ANTHROPIC_BASE_URL=xxx
export ANTHROPIC_MODEL=xxx
export ANTHROPIC_SMALL_FAST_MODEL=xxx
```

**注意：** Windows 要在 WSL 中使用，WSL 默认没有 `~/.bash_profile`，需要手动创建

```shell
$ sudo adduser newuser # new user to play with, creates in /home/newuser like your default
$ sudo cp -aR /home/newuser /mnt/c/Users/<you>
$ sudo echo "echo 'running bashrc'" >> /mnt/c/Users/<you>/newuser/.bashrc
$ sudo usermod -d /mnt/c/Users/<you>/newuser newuser
```

参考： <https://stackoverflow.com/questions/55557363/what-is-the-location-of-bashrc-on-wsl>

- 或者是设置到 `~/.bashrc`、`~/.zshrc`；最好是这 3 个都设置
- `source ~/.bashrc`

### 镜像站

镜像站，符合我们国内用户的场景，不用担心被封，无需魔法直接使用。其实就是跟官方的 Claude Code 一样，就是接口换成了中转的 API 一样。

#### **GAC Claude Code**

GAC Claude Code 镜像官网：<https://gaccode.cc/>

**安装 Claude Code 镜像站**:

```shell
npm install -g https://gaccode.com/claudecode/install --registry=https://registry.npmmirror.com
claude --version

**如果您先前安装过 Claude Code，请您务必先通过以下命令卸载！！！** (会出现镜像冲突)
npm uninstall -g @anthropic-ai/claude-code
```

在终端执行 `claude` 之后，会跳到 **GAC Claude Code 镜像站**上进行授权，所以记得先在镜像站官网登录。

### 认证相关问题

#### Token 和 API 授权冲突

```shell
⚠ Auth conflict: Both a token (ANTHROPIC_AUTH_TOKEN) and an API key (/login managed key) are set. This may lead to unexpected behavior. • Trying to use ANTHROPIC_AUTH_TOKEN? claude /logout • Trying to use /login managed key? Unset the ANTHROPIC_AUTH_TOKEN environment variable.
```

- `claude /logout` 退出登录
- `unset ANTHROPIC_AUTH_TOKEN`

#### API Error (Connection error.)

一直报错：

```shell
⎿  API Error (Connection error.) · Retrying in 1 seconds… (attempt 1/10)
⎿  TypeError (fetch failed)
⎿  API Error (Connection error.) · Retrying in 38 seconds… (attempt 9/10)
⎿  TypeError (fetch failed)
⎿  API Error (Connection error.) · Retrying in 1 seconds… (attempt 2/10)
⎿  TypeError (fetch failed)
⎿  API Error (Connection error.) · Retrying in 2 seconds… (attempt 3/10)
⎿  TypeError (fetch failed)
⎿  API Error (Connection error.) · Retrying in 5 seconds… (attempt 4/10)
```

`ANTHROPIC_BASE_URL` 访问不到，可能是你公司的内网

#### API Error (502 status code (no body))

```shell
⎿  API Error (5~02 status code (no body)) · Retrying in 1 seconds… (attempt 1/10)
⎿  API Error (502 status code (no body)) · Retrying in 1 seconds… (attempt 2/10)
⎿  API Error (502 status code (no body)) · Retrying in 2 seconds… (attempt 3/10)
⎿  API Error (502 status code (no body)) · Retrying in 5 seconds… (attempt 4/10)
⎿  API Error (502 status code (no body)) · Retrying in 8 seconds… (attempt 5/10)~
```

这种一般是网络问题，多试几次

## 相关工具安装

### gh

- [GitHub](https://cli.github.com/) 或 [GitLab](https://gitlab.com/gitlab-org/cli) CLI 用于 PR 工作流（可选）

## 优化您的终端设置

Claude Code 在您的终端正确配置时效果最佳。遵循这些指南来优化您的体验。

**支持的 shell**:

- Bash
- Zsh
- Fish

### 主题和外观 /config

Claude 无法控制您终端的主题。这由您的终端应用程序处理。您可以在入门时或通过 `/config` 命令随时将 Claude Code 的主题与您的终端匹配。

### 换行

有几个选项可以在 Claude Code 中输入换行：

- **快速转义**: 输入 `\` 然后按 Enter 创建新行
- **键盘快捷键**: 通过适当配置按 Option+Enter（Meta+Enter）

在您的终端中设置 Option+Enter：

**对于 Mac Terminal.app:**
1. 打开设置 → 配置文件 → 键盘
2. 勾选 " 使用 Option 作为 Meta 键 "

**对于 iTerm2 和 VSCode 终端:**
1. 打开设置 → 配置文件 → 键
2. 在常规下，将左/右 Option 键设置为 "Esc+"

**iTerm2 和 VSCode 用户提示**: 在 Claude Code 中运行 `/terminal-setup` 以自动配置 Shift+Enter 作为更直观的替代方案。

### 通知设置

通过适当的通知配置，永远不会错过 Claude 完成任务的时机：

### 终端铃声通知

<https://docs.anthropic.com/zh-CN/docs/claude-code/setup#%E7%BB%88%E7%AB%AF%E9%93%83%E5%A3%B0%E9%80%9A%E7%9F%A5>

### 处理大型输入

在处理大量代码或长指令时：

- **避免直接粘贴**: Claude Code 可能难以处理非常长的粘贴内容
- **使用基于文件的工作流**: 将内容写入文件并要求 Claude 读取它
- **注意 VS Code 限制**: VS Code 终端特别容易截断长粘贴

### Vim 模式

<https://docs.anthropic.com/zh-CN/docs/claude-code/setup#vim-%E6%A8%A1%E5%BC%8F>

## 其他

### 白嫖

#### anyrouter 新用户免费 $100

<https://anyrouter.top/register?aff=aXPb>

```shell
echo -e '\n export ANTHROPIC_AUTH_TOKEN=sk-...' >> ~/.bash_profile
echo -e '\n export ANTHROPIC_BASE_URL=https://anyrouter.top' >> ~/.bash_profile
echo -e '\n export ANTHROPIC_AUTH_TOKEN=sk-...' >> ~/.bashrc
echo -e '\n export ANTHROPIC_BASE_URL=https://anyrouter.top' >> ~/.bashrc
echo -e '\n export ANTHROPIC_AUTH_TOKEN=sk-...' >> ~/.zshrc
echo -e '\n export ANTHROPIC_BASE_URL=https://anyrouter.top' >> ~/.zshrc
```

> **福利提示**：通过 [专属邀请链接注册 AnyRouter](https://anyrouter.top/register?aff=aXPb) 可领 **$100 免费额度**（普通注册仅 $50）。活动限时有效！

#### 问问 code

<https://code.wenwen-ai.com/register?aff=htHF>

#### Claude Code Dashboard

<https://aicodewith.com/?invitation=GJGRYXU>

Claude Code Dashboard 注册可以赠送 1000 积分，够做好几个简单项目了。

## Ref

- Anthropic 技术团队成员 Cal Rueb 在 'Code w/ Claude' 的主题演讲
<https://x.com/shao__meng/status/1951160113835941971>
