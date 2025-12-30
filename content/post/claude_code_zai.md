---
title: "Claude Code + 智谱 AI 配置使用指南"
date: 2025-12-29T15:28:06+08:00
draft: false
tags: [linux,java,ai]
categories: [tech]
---

## 安装 Node.js

### 安装
参考[官方文档](https://nodejs.org/en/download)，使用 nvm 和 npm 方式安装 Node.js。

安装完成后验证版本：

```bash
❯ node -v
v22.19.0
❯ npm -v
10.9.3
```

### 升级

查看当前可用版本：

```bash
nvm ls-remote 
```

安装目标版本：

```bash
❯ nvm install 24
```

设为默认版本：

```bash
❯ nvm alias default 24.12.0
❯ node -v
```

升级 npm

```bash
❯ npm install -g npm@latest
❯ npm -v
```

卸载旧版本

```bash
nvm ls
nvm uninstall <version>
```

## 安装 Claude Code

```bash
# 安装
npm install -g @anthropic-ai/claude-code

# 检查当前版本
claude --version

# 升级到最新版本
claude update
```

## 配置智谱 API

注册[智谱开放平台](https://open.bigmodel.cn/)，创建[API Keys](https://bigmodel.cn/usercenter/proj-mgmt/apikeys)。

使用智谱 [Coding Tool Helper](https://docs.bigmodel.cn/cn/coding-plan/extension/coding-tool-helper) ，按照提示进行配置。

```bash
npx @z_ai/coding-helper
```

使用 Coding Tool Helper 还可以安装[智谱用量查询插件](https://docs.bigmodel.cn/cn/coding-plan/extension/usage-query-plugin)，以及套餐专属 MCP 服务
* [视觉理解 MCP](https://docs.bigmodel.cn/cn/coding-plan/mcp/vision-mcp-server)
* [联网搜索 MCP](https://docs.bigmodel.cn/cn/coding-plan/mcp/search-mcp-server)
* [网页读取 MCP](https://docs.bigmodel.cn/cn/coding-plan/mcp/reader-mcp-server)
* [开源仓库 MCP](https://docs.bigmodel.cn/cn/coding-plan/mcp/zread-mcp-server)

## 精选 MCP 服务

我觉得有价值的 MCP 服务：

* [context7](https://context7.com/) 给 AI 工具提供最新的 API 文档。

```bash
claude mcp add --transport http context7 https://mcp.context7.com/mcp \
  --header "CONTEXT7_API_KEY: YOUR_API_KEY"
```

* [Fetch MCP Server](https://github.com/modelcontextprotocol/servers/tree/main/src/fetch)

* [firecrawl-mcp-server](https://github.com/firecrawl/firecrawl-mcp-server)

* [github-mcp-server](https://github.com/github/github-mcp-server)

* [Playwright MCP server](https://github.com/microsoft/playwright-mcp)

## 使用技巧

* **危险模式**

```bash
claude --dangerously-skip-permissions
```

* **Plan Mode**

连续按 `Shift + Tab`，直到切换成 `Plan Mode`，只和你讨论需求，不会进行任何代码的修改。

* **深度思考**

在提示中加入关键词：

```text
"think about how to implement user authentication"
"think harder about this performance issue"
"ultrathink about the architecture design"

（你的需求描述）ultrathink
```

* **CLAUDE.md**

开发指南、构建命令、代码风格等关键信息，参考[Xuanwo's AGENTS.md](https://gist.github.com/Xuanwo/fa5162ed3548ae4f962dcc8b8e256bed)

* **IDE**

```bash
/ide
```

选择 Claude Code 连接的 IDE，如果选择 vs code，会自动安装 Claude Code for VS Code 插件。


* **输入多行**

```bash
/terminal-setup
```

在 macOS 上，使用 `Option + Enter` 插入换行符（Line breaks），而不是直接发送消息，达到输入多行的效果。

* **上下文处理**

```bash
# 查看当前上下文的使用
/context 

# 情况上下文状态。
# 先让 Claude 把计划与进度写入一个 .md 文件，再清空。
/clear

# 恢复会话
claude --resume
/resume

# 继续上次会话
claude --continue

# 回退对话（撤销代码更改）
/rewind
```

## 学习教程

* 官方教程 [Claude Code in Action](https://anthropic.skilljar.com/claude-code-in-action)

* [吴恩达的 Claude Code 课程](https://www.bilibili.com/video/BV1k1bBzTEF5)

* [GLM Coding 开发者社区](https://zhipu-ai.feishu.cn/wiki/TrlMwahsfihLrKkZsy0cpuTenCz)