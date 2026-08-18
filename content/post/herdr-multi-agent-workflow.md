---
title: "我如何用 Herdr 管理本地与远程的多个编码 Agent"
date: 2026-08-18T15:52:13+08:00
draft: false
tags: [ai, herdr, agent]
categories: [tech]
---

有一次，我的 macOS 重启了。当时 Claude Code 和 Codex 都跑在一台远程 Linux 服务器上，一个负责改代码，一个负责审查，旁边还有个终端留着看日志和文件。

电脑重新开机以后，我打开 Ghostty，运行 `herdr --remote <host>`。原来的 workspace、Tab 和 pane 都回来了，两个 Agent 还在继续工作。刚才消失的只是 Mac 上的 Herdr 客户端，持有终端进程的 server 一直待在 Linux 上。

这件事把 Herdr 的作用说得很直白。现在一个开发任务很少只有一段 Agent 对话。旁边往往还挂着 shell、日志、测试和开发服务器。Agent 多起来以后，我花精力的地方也跟着变了。我得知道哪个还在跑，哪个需要确认，审查走到了哪里。SSH 一旦断开，还要确认哪些进程活着。

Herdr 管的就是这圈事情。它把终端放进一个可以重新连接的环境，顺手识别里面的编码 Agent。任务仍然由我分配，两个 Agent 同时改文件的问题也要另行处理。

## 以前我盯着三个 Ghostty Tab

使用 Herdr 以前，连接一台 Linux 服务器，我至少会开三个 Ghostty Tab。

第一个通过 SSH 进去跑 Claude Code，第二个也连到同一台机器，跑 Codex。它们轮流负责实现和审查。第三个留给普通 shell，看日志、查文件，偶尔跑点别的命令。

三个 Tab 还能应付，Agent 的状态却散在各自的终端里。工作开始以后，我会在前两个 Tab 之间来回切换。看到一个停下来，还得看清它是做完了、在等输入，还是卡在权限确认。这样切得多了，巡视终端很快就成了一个下意识动作。

SSH 断线以后，事情更碎。我先重新连服务器，再进入 Claude Code 或 Codex，恢复会话，查看进度，最后让 Agent 接着做。Agent 自己的 resume 能找回对话，那组三个终端的位置和运行状态仍要重新拼。

Herdr 保留了我原来使用的终端，只在外面加了一层。后台 server 持有 pane 和其中的进程，Ghostty 里显示的是可以随时离开的 client。熟悉 tmux 的人对这一半不会陌生。Herdr 还会识别 pane 里的 Agent，把状态集中放在侧边栏。

只开一个 shell 时，直接开 shell 就够了。需要同时照看 Agent、日志和远程进程，Herdr 才开始省事。

## 先让它在本地活过一次

Herdr 的稳定版支持 Linux 和 macOS。使用官方安装器时，可以这样安装。

```bash
curl -fsSL https://herdr.dev/install.sh | sh
herdr --version
```

Homebrew、mise 和 Nix 也能安装。后续升级要继续使用原来的安装方式，`herdr update` 只服务于官方安装器管理的版本。

第一次启动不用准备 socket 或配置文件。进入一个项目目录，运行 `herdr`，后台 server 和终端 client 会一起起来。空 session 会自动创建第一个 workspace、Herdr Tab 和 pane。在 pane 里照常运行 `claude` 或 `codex`，Herdr 会根据前台进程识别 Agent。

我建议顺手安装 Claude Code 和 Codex 的官方 integration。

```bash
herdr integration install claude
herdr integration install codex
herdr integration status
```

这两个 integration 会把 Agent 自己的会话标识报告给 Herdr。server 重启以后，Herdr 才有机会调用 Agent 原生的 resume 命令。Claude Code 和 Codex 的工作状态目前仍由 screen manifest 判断。[官方 integration 文档](https://herdr.dev/docs/integrations/#how-herdr-uses-integrations)列出了两类 integration 的区别。

integration 到这里就做完了自己的事。它负责会话标识与恢复，不会教 Claude Code 怎样打开 pane、启动 Codex 或收回审查结果。要让 Claude Code 主动操作 Herdr，还要安装官方 Herdr Skill。

```bash
npx skills add herdrdev/herdr --skill herdr -g
```

Skill 是一份给 Agent 阅读的 Markdown 操作说明，不是另一个后台服务。命令都由 Claude Code 执行。Claude Code 运行在 Herdr pane 中时会拿到 `HERDR_ENV=1`，随后才能通过本地 socket 控制当前 session。`herdr --skill` 可以打印与已安装二进制版本匹配的 Skill 内容。[官方 Agent Skill 文档](https://herdr.dev/docs/agent-skill/)说明了安装方式和这条安全边界。

第一次试用不用忙着搭复杂布局。启动一个 Agent，让它跑起来，按 `ctrl+b q` detach，再执行一次 `herdr`。回来以后如果还是原来的 Agent 进程，client 和 server 的关系也就明白了。

## 我的规则是一个 Ghostty Tab 对应一台机器

我现在给 Ghostty 和 Herdr 的分工很简单。Ghostty Tab 用来选机器，进入哪台机器以后，再让 Herdr 管里面的项目和进程。

本地开发时，我在项目目录运行

```bash
herdr
```

远程开发时，我在 Mac 上运行

```bash
herdr --remote <host>
```

`<host>` 通常是 `~/.ssh/config` 里的主机名。此时本地 Herdr 是个薄客户端，通过 SSH 连接远程 server。Agent、shell 和开发进程都留在 Linux 上，我继续使用 Mac 上的终端和快捷键。Mac 剪贴板里的图片也能由 Herdr 复制成远程临时文件，再把路径粘贴进 pane。[官方远程工作说明](https://herdr.dev/docs/how-to-work/#remote-work-from-your-local-terminal)对这条路径有更完整的解释。

还有一种走法。先 `ssh <host>`，登录后在服务器上运行 `herdr`。client 和 server 都在 Linux 上，本地 Ghostty 只负责显示 SSH 终端。这条路配置少，也适合手机 SSH 客户端，代价是远程 Herdr 读不到 Mac 的图片剪贴板。

我在 Mac 上日常使用 `herdr --remote <host>`。它少了一次进入 shell 的动作，也保留了本地快捷键和图片粘贴。两条路径连接的是同一种远程 session，可以随设备切换。

![我使用 Herdr 时的本地与远程层级](https://cdn.mazhen.tech/2026/20260818184445807.svg)

进入一台机器以后，我通常给一个项目建一个 workspace。里面可以开三个 Herdr Tab，分别放 Claude Code、Codex 和日志；也可以把两个 Agent 放进同一个 `agents` Tab 的两个 pane，再把日志和普通 shell 放进 `logs` Tab。布局会变，层级不会。

workspace 装项目，Herdr Tab 区分几组工作，pane 才是实际运行进程的终端。session 则是 server 管理的整套运行环境。这样分完以后，Ghostty 的 Tab 和 Herdr 的 Tab 就不容易混在一起了。

Herdr 的鼠标操作已经够用。点击可以切换 workspace、Tab 和 pane，右键可以分割 pane，拖动边界可以调整大小。等用顺了再记 `ctrl+b` 前缀，没必要在第一次启动前背快捷键表。

## 侧边栏告诉我该去看谁

终端各自归位以后，我最常看的地方成了侧边栏。

它会识别 pane 里的前台进程，为编码 Agent 显示 `working`、`blocked`、`done`、`idle` 或 `unknown`。这些状态会向上汇总到 Herdr Tab 和 workspace。后台 Agent 等待审批时，我不用逐个打开终端确认，直接去看被标出来的 workspace。

`done` 这个名字刚开始容易让人多想一步。它表示 Agent 在后台回到可用状态，而我还没有看过它。切到对应位置以后，状态会变成 `idle`。所以 `done` 只能说明那里有结果待看，任务做得对不对还得进去检查。[官方 Agent 文档](https://herdr.dev/docs/agents/#state-rollups)把这种状态汇总作为主要工作方式。

Claude Code 和 Codex 的状态主要依赖前台进程与 screen manifest。Herdr 读取终端底部的实时画面，再用规则判断 Agent 正在运行、等待输入还是已经空闲。Agent 界面更新、包装程序和 pane 里嵌套的 tmux 都可能影响判断。

状态拿不准时，我会先打开 pane 看一眼。还可以运行

```bash
herdr agent explain <target> --verbose
```

它会列出检测来源和匹配规则。`unknown` 只说明 Herdr 现在分不清，任务究竟怎样，仍要以 pane 里的内容为准。

## Claude Code 叫来 Codex 审查

装好 Herdr Skill 以后，我不需要自己切到另一个 pane，再把审查结果搬回来。我留在 Claude Code 里，明确告诉它使用 Herdr，让 Codex 审查当前 diff。人不用搬运结果。

Claude Code 会先确认自己位于 Herdr pane 中，再查看当前布局。它在同一个 Herdr Tab 中创建 sibling pane，沿用当前工作目录。焦点不会被抢走。接下来由 Herdr 在新 pane 中启动 Codex，Claude Code 把审查任务交给它，等待状态稳定，最后读回终端输出。

整条流程可以分成五步。

1. 我在 Claude Code 中要求它使用 Herdr，让 Codex 审查当前 diff。
2. Claude Code 按 Skill 调用 `pane split`，Herdr 创建同目录的 sibling pane。
3. Claude Code 调用 `agent start` 启动 Codex reviewer，再用 `agent prompt --wait` 发送任务。
4. Codex 读取同一个 Git 工作目录中的 diff 和测试，完成后回到 `idle`、`done` 或 `blocked`。
5. Claude Code 调用 `agent read` 取得审查文本，在原会话里核对发现，再决定修改或向我汇报。

![Claude Code 通过 Herdr 让 Codex 审查当前 diff](https://cdn.mazhen.tech/2026/20260818184525492.svg)

从使用者的角度看，结果自动回到了 Claude Code。底下的动作仍然很具体。Herdr CLI 通过本地 socket 操作 Codex 所在的 PTY，写入提示，等待生命周期状态，再把 `agent read` 读到的终端文本交回 Claude Code。Codex 没有直接向 Claude Code 推送结构化消息，两个 Agent 也没有自动共享上下文。

这套流程对应的命令并不神秘。下面这段通常由 Claude Code 按 Skill 执行，系统需要安装 `jq`。

```bash
split=$(herdr pane split --current \
  --direction right \
  --cwd "$PWD" \
  --no-focus)

review_pane=$(printf '%s\n' "$split" |
  jq -r '.result.pane.pane_id')

herdr agent start reviewer \
  --kind codex \
  --pane "$review_pane"

herdr agent prompt reviewer \
  "Review the current diff. Do not edit files. Report only actionable findings." \
  --wait \
  --timeout 120000

herdr agent read reviewer \
  --source recent-unwrapped \
  --lines 120
```

`agent start` 不会创建 pane。它只负责在已有的空闲 pane 中启动 Agent，所以前面的 `pane split` 不能省。`agent prompt --wait` 等待的是生命周期状态。Codex 如果进入 `blocked`，Claude Code 还要先读状态和输出，遇到权限或产品判断时仍可能回来问我。

两个 Agent 在这里使用同一个 Git 工作目录。我给自己的规则是 Claude Code 写，Codex 只读审查。只读要求要写进给 Codex 的提示，Herdr 本身不会强制执行。我还没有在这套工作流里使用 worktree，以后真要让两个 Agent 同时写，再用独立 worktree 隔开。

这里没有一场值得渲染的审查事故。它就是我每天会重复的流程。Claude Code 干完以后叫来 Codex，等它看完，再把结果带回原来的会话。旁边的 pane 一直可见，我随时可以切过去看，但不必手工在两个 Agent 之间传话。

## SSH 断了，任务还在

远程模式里，server 运行在 Linux 上，Mac 只是 client。只要远程 server 还活着，`ctrl+b q` detach、关闭 Ghostty 和 SSH 断线都不会结束 pane 中的进程。Mac 重启也是同样的结果，因为被重启的是客户端那台机器。

重新运行 `herdr --remote <host>`，我会回到同一个远程 session。开头那次 macOS 重启以后，两个远程 Agent 还在工作，靠的就是这条边界。

`herdr server stop` 是另一回事。server 持有的 shell、Agent、测试和开发服务器都会结束。下次启动时，Herdr 可以恢复 workspace、Tab、pane、工作目录和布局，普通进程已经不在。当前官方 integration 如果保存过 Agent 的原生会话标识，Herdr 可以调用 Claude Code 或 Codex 自己的 resume 能力恢复对话。这属于重新启动进程，和 detach 以后原进程一直活着有区别。[官方 session 状态表](https://herdr.dev/docs/session-state/#what-survives)列出了几种恢复方式。

本地工作也遵循这条规则。本地 server 跑在 Mac 上，关掉 Ghostty 不会结束任务，重启 macOS 会结束本机进程。远程任务能跨过 Mac 重启，只因为 server 位于 Linux。

### 手机上临时看一眼

这部分我还没有亲自用过。根据[官方手机工作流](https://herdr.dev/docs/how-to-work/#work-from-your-phone)，手机不需要 Herdr App。用 SSH 客户端登录运行 Agent 的服务器，再执行 `herdr`，就能打开同一个持久 session。Herdr 会在窄终端中换成单列布局，可以查看 Agent、切换 workspace，也可以进入 pane。

```bash
ssh you@server
herdr
```

这条路径适合临时查看状态和回答问题。官方没有提供移动端推送、专用审批界面或网页控制台，它也不等于一套完整的移动开发环境。

## 升级这件事我不敢赌

我以前升级 Herdr，会先把它完全退出，再安装新版本。这个办法最稳，内部任务也会一起结束。写这篇文章时，我特意查了有没有不中断任务的升级方式。

官方安装器管理的版本可以运行

```bash
herdr update
```

官方文档说明，协议兼容的旧 server 可以继续运行；需要更换 server 时，升级程序会询问是否停止 session。只要确认停止，pane 里的进程就会结束。重要任务还在运行时，更稳妥的选择是拒绝停止，等 Agent 做完或者走到容易恢复的位置再升级。

Herdr 还提供了一条实验路径。

```bash
herdr update --handoff
```

live handoff 会尝试把 pane PTY、进程、Agent 标识和 session 状态交给新 server。它只适用于 Herdr 自带 updater 管理的安装，结果属于 best effort。正在进行的 CLI 或 API 请求、`wait`、订阅和 pane 间消息仍可能中断。Homebrew、mise 和 Nix 安装也不能走这条更新路径。[官方 live handoff 说明](https://herdr.dev/docs/session-state/#live-handoff)列出了这些限制。

升级前，可以先在 Herdr 外层运行

```bash
herdr status --json
```

这里能看到 client、server、protocol、`restart_needed` 和 `live_handoff` 能力。我目前还是按保守方式处理。任务不能断就延后重启 server；能接受实验风险时再试 handoff，完成以后重新检查版本、pane 输出和 Agent 状态。

## Herdr 管到哪里

Herdr 管终端和 Agent 状态，项目里的协作关系仍留在 Git 与任务说明里。

两个 pane 指向同一目录时，它们看到同一份文件。Herdr 允许两个 Agent 同时写，也不会自动合并改动。我目前保留一个写入者，审查者只读。以后需要同时写入，再用独立 worktree、分支和文件所有权把范围划开。

同一个 workspace 也不会让 Agent 自动共享上下文。Codex 能看到文件、Git diff、测试结果和我交给它的任务。Claude Code 为什么做出某项设计，仍要靠项目文档、提交信息或审查说明传下去。

状态只能引导人去哪里看。`done` 说明有结果待看，没有证明结果正确。`blocked` 说明界面像是在等待输入，批准什么仍要读上下文。碰到删除、部署、凭据和高风险修改，仍然需要进入 pane 确认。

远程连接沿用 OpenSSH 的身份验证。`herdr --remote` 不要求在 SSH 配置中启用 `ForwardAgent yes`。远程 Agent 如果需要借用本地密钥访问 Git，再单独判断是否接受 agent forwarding 的风险。

Herdr 的适用范围因此很清楚。一个 shell 用不上它。项目里同时跑着两个 Agent，旁边还有日志或测试，或者工作全在远程 Linux 上，就可以让 Herdr 接手终端的寿命和状态。

Mac 重启以后，我重新打开 Ghostty，敲下 `herdr --remote <host>`。原来的位置还在，任务也还在，不用再把三个 SSH Tab 从头拼一遍。对我来说，这已经够了。
