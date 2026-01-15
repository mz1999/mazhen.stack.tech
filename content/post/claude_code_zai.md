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

使用 Coding Tool Helper 还可以安装[智谱用量查询插件](https://docs.bigmodel.cn/cn/coding-plan/extension/usage-query-plugin)，以及套餐专属 MCP 服务：

* [视觉理解 MCP](https://docs.bigmodel.cn/cn/coding-plan/mcp/vision-mcp-server)
* [联网搜索 MCP](https://docs.bigmodel.cn/cn/coding-plan/mcp/search-mcp-server)
* [网页读取 MCP](https://docs.bigmodel.cn/cn/coding-plan/mcp/reader-mcp-server)
* [开源仓库 MCP](https://docs.bigmodel.cn/cn/coding-plan/mcp/zread-mcp-server)

## 精选 MCP 服务

* [context7](https://context7.com/) 给 AI 工具提供最新的 API 文档。

```bash
claude mcp add --transport http context7 https://mcp.context7.com/mcp \
  --header "CONTEXT7_API_KEY: YOUR_API_KEY" -s user
```

* [Fetch MCP Server](https://github.com/modelcontextprotocol/servers/tree/main/src/fetch)

* [firecrawl-mcp-server](https://github.com/firecrawl/firecrawl-mcp-server)

* [github-mcp-server](https://github.com/github/github-mcp-server)

* [Playwright MCP server](https://github.com/microsoft/playwright-mcp)

```bash
claude mcp add playwright npx @playwright/mcp@latest
```

* [Chrome DevTools MCP](https://github.com/ChromeDevTools/chrome-devtools-mcp)

```bash
claude mcp add chrome-devtools --scope user npx chrome-devtools-mcp@latest
```

## 精选 Skills

### [官方 skills](https://github.com/anthropics/claude-plugins-official)

**推荐插件列表**

* [frontend-design](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/frontend-design) 
* [ralph-wiggum](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/ralph-wiggum) Claude Code 之父 [Boris Cherny](https://x.com/bcherny) 推荐的 [skill](https://x.com/bcherny/status/2007179832300581177)，内部是一个循环，不停的根据任务说明书，一遍又一遍地改进工作，直到彻底完成，可以让 Claude Code“通宵干活”。

### [superpowers](https://github.com/obra/superpowers)

superpowers 一套专为 AI 编程 Agent 设计的全流程工作流系统。

```bash
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

### [dev-browser](https://github.com/SawyerHood/dev-browser)

让 Claude 控制浏览器，进行测试和验证。功能和 `Playwright MCP` 类似，但据说比 `Playwright MCP` 强。

```bash
/plugin marketplace add sawyerhood/dev-browser
/plugin install dev-browser@sawyerhood/dev-browser
```

## 使用技巧

### **危险模式**

```bash
claude --dangerously-skip-permissions
```

### **Plan Mode**

连续按 `Shift + Tab`，直到切换成 `Plan Mode`，只和你讨论需求，不会进行任何代码的修改。

### **深度思考**

在提示中加入关键词：

```text
"think about how to implement user authentication"
"think harder about this performance issue"
"ultrathink about the architecture design"

（你的需求描述）ultrathink
```

### **CLAUDE.md**

开发指南、构建命令、代码风格等关键信息，参考[Xuanwo's AGENTS.md](https://gist.github.com/Xuanwo/fa5162ed3548ae4f962dcc8b8e256bed)

### **IDE**

```bash
/ide
```

选择 Claude Code 连接的 IDE，如果选择 vs code，会自动安装 Claude Code for VS Code 插件。


### **输入多行**

~~在 macOS 上，使用 `Option + Enter` 插入换行符（Line breaks）。可用通过设置，将换行操作绑定到 `Shift + Enter`~~。 

```bash
/terminal-setup
```
Claude Code 2.1.0 [最新发布](https://x.com/bcherny/status/2009072293826453669)，使用 `Shift+Enter` 键来插入换行符，在 **iTerm2**, **WezTerm**, **Ghostty** 和 **Kitty** 上无需进行任何设置。

### **上下文处理**

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

### 规划/头脑风暴
如果你只有一个初步的想法，还没有详细的规划，可以先使用 **Superpowers** 的 `/superpowers:brainstorm` 进行头脑风暴，和 Claude Code 讨论，一步步梳理想法，清晰目标，最终完成详细的产品规划。

有了规划，可以继续使用 `/superpowers:write-plan` 创建具体的实现方案，最后 `/superpowers:execute-plan`，方案落地执行。

另外，Claude code 内置的 **AskUserQuestion** 工具也很有用，它的作用是，AI 在遇到模糊不清的情况或需要决策时，AI 会主动暂停，然后不断向你提问，目标清晰后再继续执行。

你也可以主动触发 **AskUserQuestionTool** ，例如 [Thariq](https://x.com/trq212) 分享的[方法](https://aiengineerguide.com/blog/improvise-spec-claude-code/)，开始动手前，让 **AskUserQuestionTool** 对你的规划进行“面试“：

```text
read this @SPEC.md and interview me in detail using the AskUserQuestionTool about literally anything: technical implementation, UI & UX, concerns, tradeoffs, etc. but make sure the questions are not obvious

be very in-depth and continue interviewing me continually until it's complete, then write the spec to the file
```

结合 **Superpowers**，我们可以在 **Superpowers** 完成设计后，让 **AskUserQuestionTool** 面试提问，相信经过 **AskUserQuestionTool** 的锤炼，产品设计更加清晰，符合你的预期。

### 参考 spec-kit

[Spec Kit](https://github.com/github/spec-kit) 是一套很重的规则，很多人不建议使用，但参考部分技巧还是可取的，例如 [yetone](https://x.com/yetone) 在[推文](https://x.com/yetone/status/2007727272497930468)中提到，可参考[The Nine Articles of Development](https://github.com/github/spec-kit/blob/main/spec-driven.md#the-nine-articles-of-development)的前三个规则。

如何使用？可以将规则定义放在一个固定的路径，然后在 http://AGENTS.md/CLAUDE.md 中引用这个路径。

### UI/UX

关于 UI/UX 设计，我还没有太多经验，不知道怎么生成令人惊艳的界面。目前只是在产品设计规划阶段，细化对界面的需求：

```text
我希望使用 shadcn/ui，主题为 npx shadcn@latest add https://tweakcn.com/r/themes/claude.json，页面布局参考常见的 SaaS 软件，左右两栏布局，左边是菜单栏。
```

上面的提示让 Claude Code 参考 [tweakcn.com](https://tweakcn.com/) 预定义的样式生成界面，也可以直接将[theme](https://tweakcn.com/editor/theme)的 `css` 代码直接复制给 Claude code。

据说可以用 [frontend-design](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/frontend-design) 改进前端设计，打算先学习  [Frontend Aesthetics: A Prompting Guide](https://github.com/anthropics/claude-cookbooks/blob/main/coding/prompting_for_frontend_aesthetics.ipynb)。是否可以让 `frontend-design` 根据需求直接生成页面？

另外可以参考一些生成前端页面的 prompt 或 skills：

1. [designprompts.dev](https://www.designprompts.dev/) 和 [designprompt.vercel.app](https://designprompt.vercel.app/) 提供了多种前端风格的 prompt，可以直接复制使用。
2. [UI UX Pro Max](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) UI/UX skill，包含了多种前端样式。
3. [ui-skills](https://www.ui-skills.com/) 一个专门用来设计界面 UI 的 Claude Skill。

### Stitch

[Stitch](https://stitch.google.com/) ，Google 出品的 UI 设计器，必须拎出来作为单独一个小节介绍。可以根据你的描述内容，直接输出高保真效果图，并能导出 HTML 页面。

我觉得可以将前面 Superpowers 生成的产品功能规划，喂给 Stitch，让它生成界面效果图，导出 HTML 页面，然后让 Claude Code 根据页面，填充功能。

### 前端测试

配置好 `Playwright MCP server`，就可以在 Claude Code 中明确要求使用 **Playwright** 进行前端功能测试。

~~如果是在本地开发，也可以使用 [dev-browser](https://github.com/SawyerHood/dev-browser) 进行前端测试。~~

**Chrome DevTools MCP** 虽然只支持 Chrome，但它支持更多的性能分析和浏览器调试功能，优先推荐使用。

### 状态栏定制

目前有两个定制 statusline 的小工具，都能实时显示使用的模型，context usage 等信息。

1. [Claude HUD](https://github.com/jarrodwatts/claude-hud)

```bash
# Step 1: Add the marketplace
/plugin marketplace add jarrodwatts/claude-hud

# Step 2: Install the plugin
/plugin install claude-hud

# Step 3: Configure the statusline
/claude-hud:setup
```

2. [Claude Powerline](https://github.com/Owloops/claude-powerline)

[Claude Powerline](https://github.com/Owloops/claude-powerline) 的配置更简单，在 `.claude/settings.json` 中添加如下配置即可：

```json
  "statusLine": {
    "type": "command",
    "command": "npx -y @owloops/claude-powerline@latest --style=powerline --theme=gruvbox"
  },
```

## 学习教程

* 官方教程 [Claude Code in Action](https://anthropic.skilljar.com/claude-code-in-action)

* [吴恩达的 Claude Code 课程](https://www.bilibili.com/video/BV1k1bBzTEF5)

* [GLM Coding 开发者社区](https://zhipu-ai.feishu.cn/wiki/TrlMwahsfihLrKkZsy0cpuTenCz)