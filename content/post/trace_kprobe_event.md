---
title: "手动创建 kprobe 事件追踪 bind 系统调用"
date: 2025-09-25T10:24:48+08:00
draft: false
tags: [java,linux,trace,perf,bcc,kprobe]
categories: [tech]
---
在前面的文章 [追踪定位 Java 进程的 Socket 创建](https://mazhen.tech/p/%E8%BF%BD%E8%B8%AA%E5%AE%9A%E4%BD%8D-java-%E8%BF%9B%E7%A8%8B%E7%9A%84-socket-%E5%88%9B%E5%BB%BA/)中，介绍了如何使用 `async-profiler` 追踪 `tracepoint` 事件 `sys_enter_bind`，定位 Java 中网络监听端口的创建。但部分系统因内核配置限制可能不支持该事件。本文将详细介绍如何通过手动创建`kprobe`事件，实现对`bind`系统调用的精准追踪。

## 问题背景：系统不支持 syscalls:sys_enter_bind tracepoint

当执行`perf list | grep syscalls`时，若输出中没有`syscalls:sys_enter_bind`，说明系统内核未启用`CONFIG_SYSCALL_TRACEPOINTS`配置（可通过`grep CONFIG_SYSCALL_TRACEPOINTS /boot/config-$(uname -r)`验证）。此时无法直接使用`syscalls`系列跟踪点，需通过`kprobe`动态探测内核函数实现追踪。


## 解决方案：手动创建 kprobe 事件追踪 bind 系统调用

`kprobe`是内核提供的动态调试机制，可在任意内核函数的入口/出口插入探测点。`bind`系统调用的内核实现函数通常为`sys_bind`或`__sys_bind`，我们可通过`kprobe`追踪该函数。

### 步骤 1：确认 bind 系统调用的内核实现函数

首先需确定当前内核中`bind`系统调用对应的函数名，执行以下命令（需 root 权限）：

```shell
grep -r "bind" /proc/kallsyms | grep -E "sys_bind|__sys_bind"
```

示例输出（不同系统可能不同）：
```
ffff000008aabbe0 T __sys_bind
```

记录函数名（如`__sys_bind`），后续将基于此创建探测点。

### 步骤 2：手动创建 kprobe 事件

`kprobe`事件需通过内核`tracefs`接口创建，操作如下：

1. **进入 tracefs 目录**（内核跟踪接口目录）：

   ```shell
   cd /sys/kernel/debug/tracing
   ```

2. **创建 kprobe 事件**：

   使用`echo`命令向`kprobe_events`文件写入事件定义，格式为：

   ```shell
   # p:表示在函数入口插入探测点，kprobes/bind_kprobe 为事件名，__sys_bind 为目标内核函数
   echo 'p:kprobes/bind_kprobe __sys_bind' > kprobe_events
   ```

3. **验证事件创建成功**：

   查看`kprobe_events`文件确认事件已添加：

   ```shell
   cat kprobe_events
   # 输出应包含：p:kprobes/bind_kprobe __sys_bind
   ```

### 步骤 3：启用 kprobe 事件

创建事件后需手动启用，否则`perf`无法捕获：

```shell
# 启用事件（1表示启用，0表示禁用）
echo 1 > events/kprobes/bind_kprobe/enable
```

### 步骤 4：用 perf 追踪 bind 系统调用

事件启用后，`perf`可直接追踪该`kprobe`事件，操作如下：

1. **启动 perf 追踪**（在任意目录执行）：

   ```shell
   # -e 指定追踪 kprobes:bind_kprobe 事件，-g 记录调用栈，-a 追踪所有 CPU
   perf record -e kprobes:bind_kprobe -g -a
   ```
   此时`perf`会持续运行，等待`bind`系统调用触发。

2. **触发 bind 操作**：
   在另一个终端启动目标程序（如需要追踪的 Java 进程），执行可能触发`bind`的操作（如启动网络服务）。

3. **停止追踪并分析结果**：

   - 按`Ctrl+C`停止`perf`，生成`perf.data`文件；
   - 执行`perf report`查看结果，可看到触发`bind`调用的进程、调用栈等信息：

     ```shell
     perf report
     ```

### 步骤 5：清理 kprobe 事件

追踪完成后，需清理创建的`kprobe`事件，避免占用系统资源：

1. **禁用事件**：
   ```shell
   cd /sys/kernel/debug/tracing
   echo 0 > events/kprobes/bind_kprobe/enable
   ```

2. **删除事件**：

   ```shell
   # 使用相对路径删除（在 tracing 目录下）
   echo '-:kprobes/bind_kprobe' > kprobe_events
   ```

3. **验证清理结果**：

   ```shell
   cat kprobe_events  # 输出为空，说明事件已删除
   ```


## 关键注意事项

1. **内核配置要求**：
   系统需启用`CONFIG_KPROBE_EVENTS`和`CONFIG_DEBUG_FS`（可通过`grep -E "CONFIG_KPROBE_EVENTS|CONFIG_DEBUG_FS" /boot/config-$(uname -r)`验证，需均为`y`）。

2. **权限问题**：
   所有操作需 root 权限，且`tracefs`需已挂载（默认挂载，若未挂载可执行`mount -t tracefs nodev /sys/kernel/debug/tracing`）。

## 总结

当系统不支持`syscalls:sys_enter_bind`跟踪点时，通过手动创建`kprobe`事件，可间接实现对`bind`系统调用的追踪。该方法依赖内核动态探测机制，无需重新编译内核，适用于多数 Linux 发行版，是调试网络程序绑定端口行为的有效手段。