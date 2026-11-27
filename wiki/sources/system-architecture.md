# 来源笔记：System Architecture

> 适用版本：QNX SDP 8.0 · 纳入日期：2026-11-27 · 范围：选定章节

[官方指南](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.sys_arch/topic/about.html)解释微内核和系统服务的设计。本轮只核对以下章节；本地文件在 `raw/qnx-sdp-8.0/system-architecture/`。

| 已读章节 | 本轮提取的信息 | 关联 Wiki |
| --- | --- | --- |
| [Interprocess Communication](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.sys_arch/topic/ipc.html) | 消息传递是主要 IPC 形式；`MsgSend()`、`MsgReceive()`、`MsgReply()` 形成同步的数据复制交互 | [消息传递](../topics/message-passing.md) |
| [Threads and processes](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.sys_arch/topic/kernel_THREADSANDPRO.html) | 线程是执行和调度单位；进程提供地址空间 | [进程与线程](../topics/processes-threads.md) |
| [Resource Managers](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.sys_arch/topic/resource.html) | 资源管理器是可独立运行的用户态进程 | [资源管理器](../topics/resource-managers.md) |

本轮没有审阅整本架构指南。文件系统、网络架构、动态链接等章节仍属待读范围。
