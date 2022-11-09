---
title: "配置rustup和Cargo国内源"
date: 2022-09-30T22:36:25+08:00
draft: false
tags: [rust]
categories: [tech] 
---

Rust开发生态最重要的两个工具

* [rustup](https://rustup.rs)  Rust 官方的跨平台安装工具
* [Cargo](https://doc.rust-lang.org/cargo/index.html)  Rust 构建系统和包管理器

它们的下载源都位于国外，为了改善在国内的使用体验，可以为它们配置国内源。

**rustup 国内源**

目前国内 Rust 工具链镜像源有清华大学源、中国科学技术大学源、上海交通大学源等，以[清华大学源](https://mirrors.tuna.tsinghua.edu.cn/help/rustup/)为例，设置环境变量：

```bash
export RUSTUP_DIST_SERVER=https://mirrors.tuna.tsinghua.edu.cn/rustup
export RUSTUP_UPDATE_ROOT=https://mirrors.tuna.tsinghua.edu.cn/rustup/rustup
```

**crates.io 国内源**

Cargo 默认的源服务器为 crates.io，同样可以配置为国内的镜像源，以[清华大学源](https://mirrors.tuna.tsinghua.edu.cn/help/crates.io-index.git/)为例，编辑 `~/.cargo/config` 文件，添加以下内容：

```bash
[source.crates-io]
replace-with = 'tuna'

[source.tuna]
registry = "https://mirrors.tuna.tsinghua.edu.cn/git/crates.io-index.git"
```
这样可加快 Cargo 读取软件包索引的速度。
