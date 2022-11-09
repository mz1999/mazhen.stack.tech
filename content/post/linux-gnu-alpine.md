---
title: "Linux, GNU/Linux以及Alpine Linux"
date: 2022-09-30T10:23:23+08:00
draft: false
tags: [linux,kernel]
categories: [tech]
---

[原文链接](https://twitter.com/iximiuz/status/1560977903227383810)

**Linux** 是内核，由Linus在90年代创造。不包括驱动程序，内核本身在编译时只有几兆字节。

**GNU**是一个自由软件的大集合，可以和操作系统一起使用，包括你非常熟悉的 `grep`、`sed`、`gcc`等等。**GNU** 还包括 `glibc`，C 语言标准库的实现。

**GNU/Linux** 是任何基于 GNU 集合的Linux发行版（内核+用户态应用）。Debian、Ubuntu、CentOS，甚至RHEL在技术上都是 **GNU/Linux**。因为有共同的C语言标准库和系统工具，通常你可以在 GNU/Linux 发行版之间跳来跳去，不会有什么麻烦。

**Alpine Linux** 是另一个类型 Linux 发行版，它不是基于 GNU 集合。

为了替代 GNU，Alpine 使用：

- **BusyBox**：一个小型软件套件 (~2MB)，在单个可执行文件中提供了多个 Unix 实用程序。
- **musl**：一种现代且更强大的 C 标准库实现

因此，**Alpine Linux** 不是 Debian 或 CentOS 等 GNU/Linux 发行版的直接替代品，`musl` 的行为可能与 `glibc` 不同！
