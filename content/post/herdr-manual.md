---
title: "Herdr 操作手册"
date: 2026-08-03T17:10:48+08:00
draft: false
tags: [ai, linux]
categories: [tech]
---

## 几个要先搞懂的概念

### 层级结构

从大到小，四层嵌套：

![Herdr 层级结构](https://cdn.mazhen.tech/2026/20260803171237461.svg)


### Client / Server 分离

为什么 detach 不死？

- **Server**:后台进程，**真正持有 agent**。你关终端它还在。
- **Client**:终端 UI，只是"显示器 + 键盘",attach 到 server。
- `Ctrl+B Q`(detach)→ 客户端断开，**server 继续**。
- 再敲 `herdr` → reattach，界面原样恢复。

**记住**:agent 跑在 server 里，不是跑在你的终端里。这是 SSH 断线不丢 agent 的根本原因。

![Client/Server 与 detach/reattach](https://cdn.mazhen.tech/2026/20260803171359559.svg)

### 两层 Tab 不要混淆 (Ghostty 用户必读)

```
Ghostty(外层)        ← Cmd+1/2/3 切 Ghostty 原生 tab
  └─ Herdr(内层)     ← Ctrl+B 然后 1/2/3 切 Herdr 的 tab
       └─ Pane
```

`Cmd+数字` 是 Ghostty 的，Herdr 抢不到，所以 Herdr 用 `prefix+ 数字`。两层各管各的。

## 两端安装

### Mac 本机

```bash
curl -fsSL https://herdr.dev/install.sh | sh
herdr --version   # 验证
```

### Linux 服务器

SSH 上去后：
```bash
curl -fsSL https://herdr.dev/install.sh | sh
herdr --version
```

升级 (用官方脚本装的才适用):
```bash
herdr update
```

## 本地工作流

### 启动

```bash
herdr     # 启动 server + 打开 client(已存在则 attach)
```

### 干活

在 pane 里像平时一样启动 agent:
```bash
claude           # 启动 Claude Code
# 或多开
# Ctrl+B, V → 右切新 pane → 再敲 claude
```

侧边栏自动显示状态。

### 暂时离开

`Ctrl+B` 松开，再按 `Q` → detach,agent 继续跑。

### 回来

```bash
herdr     # reattach
```

### 彻底结束 (慎用，杀全部 agent)

```bash
herdr server stop
```

## 远程工作流 (SSH 场景)

![远程两种模式对比](https://cdn.mazhen.tech/2026/20260803171634989.svg)

这是你最大的收益区：**SSH 断线不丢 agent**。两种模式，按场景选。

### 模式 A:`herdr --remote`(Mac 日常用，推荐)

```bash
herdr --remote workbox
```

**特点**:
- Mac 当瘦客户端，SSH 连服务器，把服务器 Herdr 界面拉到本地
- **agent 跑在服务器**,但你在 Mac 上操作
- **图片粘贴能用**(Herdr 把 Mac 剪贴板图片桥接到服务器)★裸 SSH 做不到
- 用 Mac 本地的快捷键配置

**适合**:你在 Mac 上日常连服务器干活，尤其要给 Claude 发截图时。

### 模式 B:先 SSH 再 herdr(tmux 式)

```bash
ssh workbox
herdr
```

**特点**:
- 整个 Herdr 在服务器上跑，终端只是显示器
- 图片粘贴不行
- 界面自适应窄屏

**适合**:手机/平板 SSH 连过去时、或临时简单连一下。

### 配置 SSH 别名

编辑 Mac 的 `~/.ssh/config`:
```
Host workbox
  HostName server.example.com
  User you
  Port 2222
  ForwardAgent yes        # ★让服务器能借你 Mac 的 SSH 密钥做 git
  ServerAliveInterval 60  # 防 SSH 断线
```

之后直接：
```bash
herdr --remote workbox
```

### 典型的一天

```
上午  Mac: herdr --remote workbox
      → 连服务器,开 3 个 Claude 并行
开会  Ctrl+B Q → detach,服务器 agent 继续跑
下午  Mac: herdr --remote workbox → reattach,接着干
晚上  手机: ssh workbox → herdr → 窄屏看状态、做决策
```

## 日常操作速查

### Prefix 键 = `Ctrl+B`(先按，松开，再按动作键)

### 5 个必记

| 动作 | 快捷键 |
|---|---|
| 新建 tab | `Ctrl+B` → `C` |
| 右切 / 下切 pane | `Ctrl+B` → `V` / `Ctrl+B` → `-` |
| pane 间移动 | `Ctrl+B` → `H/J/K/L`(左/下/上/右，vim 风格) |
| workspace 导航 | `Ctrl+B` → `W` |
| detach(保留 agent) | `Ctrl+B` → `Q` |

### Tab 操作

| 动作 | 快捷键 |
|---|---|
| 下一个 / 上一个 tab | `Ctrl+B` → `N` / `P` |
| 跳到第 1–9 个 tab | `Ctrl+B` → `1..9` |
| 重命名 tab | `Ctrl+B` → `Shift+T` |
| 关闭 tab | `Ctrl+B` → `Shift+X` |

### Pane 操作

| 动作 | 快捷键 |
|---|---|
| 放大/还原当前 pane(zoom) | `Ctrl+B` → `Z` |
| 关闭 pane | `Ctrl+B` → `X` |
| 交换 pane 位置 | `Ctrl+B` → `Shift+H/J/K/L` |
| 调整大小模式 | `Ctrl+B` → `R` |
| 复制模式 (翻历史) | `Ctrl+B` → `[` |

### Workspace 操作

| 动作 | 快捷键 |
|---|---|
| 新建 workspace | `Ctrl+B` → `Shift+N` |
| 重命名 workspace | `Ctrl+B` → `Shift+W` |
| 关闭 workspace | `Ctrl+B` → `Shift+D` |
| 全局跳转选择器 | `Ctrl+B` → `G` |
| 切换侧边栏显隐 | `Ctrl+B` → `B` |

### 鼠标操作

- 点击 pane / tab / workspace / agent → 切换
- 拖动分割边框 → 调整 pane 大小
- 选中文字 → 选中
- 右键 → 菜单
- 拖选 → 直接复制 (不用进 copy mode)

### 查看所有快捷键 (忘了随时查)

```
Ctrl+B → ?
```

## Agent 状态管理

![Agent 五状态机](https://cdn.mazhen.tech/2026/20260803171832272.svg)

### 五种状态

| 状态 | 含义 | 你该做什么 |
|---|---|---|
| 🔴 `blocked` | **需要你**:等输入/审批/决策 | **立刻处理** |
| 🔵 `working` | 正在干活 | 别打扰 |
| ⚪ `done` | 干完了，**你还没看过** | 去看结果 |
| 🟢 `idle` | 完成/等待，**已被你看过** | 可派新活 |
| ❓ `unknown` | Herdr 拿不准 | 主动检查 |

### done vs idle 的区别

- `done`:后台默默干完了，你没看过 → 提醒"有成果待领取"
- `idle`:你看过了 → 已知悉，待命

**怎么算"看过"**:聚焦到那个 tab，或用 focus 命令 targeting 该 pane/agent。**CLI 读取不算看过**(防止 agent 自动操作误清状态)。

### 检测原理

Herdr 通过三方式识别 pane 里的 agent:
1. 前台进程
2. 屏幕特征 (screen manifests)—— 认审批框、加载态等
3. 可选集成插件 (装了更准)

**零配置**识别 Claude Code / Codex / OpenCode / Amp 等 20+ agent。

### 不要在 Herdr pane 里再开 tmux

会**屏蔽 agent 状态检测**(tmux 把内部输出吃掉)。要让状态功能生效，agent 必须直接跑在 Herdr pane 里。

## 多 Agent 并行

### 什么时候该多开

| 能独立拆分的任务 | 多开价值 |
|---|---|
| 全栈前后端 (可并行) | ✅ |
| 多功能/多分支 (配 git worktree) | ✅ |
| 写 + 测试 分离 | ✅ |
| 写 + 审查 分工 | ✅ |
| 多个不相关 bug 并排 | ✅ |
| 一个任务串行就够 | ❌ 别折腾 |

### 多开的正确姿势

- **用 git worktree 物理隔离**:每个 agent 绑一个分支的工作目录，改的不是同一份代码
- **agent 各干各的，不要让它们同时改同一份代码**(merge 冲突地狱)
- **真并行**(同时跑),不是串行流水线

### 创建 worktree 隔离的 workspace

```bash
herdr worktree create   # 把分支映射到独立 workspace
```

## 退出与恢复

| 你想要 | 命令 | agent 命运 |
|---|---|---|
| 暂时离开/下班/开会 | `Ctrl+B` → `Q` | ✅ 继续跑 |
| 回来继续 | `herdr`(或 `herdr --remote workbox`) | ✅ reattach |
| 彻底关掉 (不要 agent 了) | `herdr server stop` | ❌ 全部杀掉 |

**日常退出永远用 `Ctrl+B Q`,只有确定不要 agent 才 `herdr server stop`。**

## 配置与定制

### 配置文件位置

```
~/.config/herdr/config.toml
```

### 改 prefix 键

```toml
[keys]
prefix = "ctrl+a"   # 比如改成 Ctrl+A
```

### 无 prefix 直按键 (推荐配 Ctrl+Alt，跨终端安全)

```toml
[keys]
focus_pane_left = ["prefix+h", "ctrl+alt+h"]
focus_pane_down = ["prefix+j", "ctrl+alt+j"]
focus_pane_up = ["prefix+k", "ctrl+alt+k"]
focus_pane_right = ["prefix+l", "ctrl+alt+l"]
previous_tab = ["prefix+p", "ctrl+alt+["]
next_tab = ["prefix+n", "ctrl+alt+]"]
new_tab = ["prefix+c", "ctrl+alt+c"]
zoom = ["prefix+z", "ctrl+alt+z"]
```

> 官方推荐 `Ctrl+Alt` 家族：几乎不被任何终端/OS 占用。但要避开：
> - `Ctrl+Alt+方向键`(Ghostty 占)
> - `Ctrl+Alt+T`(Ubuntu 开终端)
> - `Ctrl+Alt+F1..F12`(Linux 虚拟控制台)

### 关闭鼠标捕获

```toml
[ui]
mouse_capture = false
```

### 远程模式：用裸 ssh 不生成临时配置

```toml
[remote]
manage_ssh_config = false
```

### Named Session(一般不用，需要完全隔离时才用)

```bash
herdr session list
herdr session attach work
herdr session attach side-project
herdr session stop work
```

**官方建议：先用 workspace，不够了再用 named session。**