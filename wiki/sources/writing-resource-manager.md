# 来源笔记：Writing a Resource Manager

> 适用版本：QNX SDP 8.0 · 纳入日期：2026-11-27 · 范围：选定章节

[官方指南](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.resmgr/topic/about.html)介绍资源管理器的编写方式。本轮只核对以下章节；本地文件在 `raw/qnx-sdp-8.0/resource-managers/`。

| 已读章节 | 本轮提取的信息 | 关联 Wiki |
| --- | --- | --- |
| [What Is a Resource Manager?](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.resmgr/topic/overview.html) | 注册路径名的用户态进程，可表示实体或虚拟设备 | [资源管理器](../topics/resource-managers.md) |
| [Put a name into the namespace](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.resmgr/topic/skeleton_RegisterName.html) | 用 `resmgr_attach()` 注册路径名前缀 | [资源管理器](../topics/resource-managers.md) |
| [Layers in a resource manager](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.resmgr/topic/skeleton_RESMGR_layers.html) | `iofunc`、`resmgr`、`dispatch` 和线程池层各自的职责 | [资源管理器](../topics/resource-managers.md) |
| [I/O messages](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.resmgr/topic/fleshing_out_IO_messages.html) | `open()` 建立的上下文供后续 `_IO_READ` 等消息使用 | [资源管理器](../topics/resource-managers.md) |

本轮未覆盖完整的错误处理、多线程、权限和设备控制实现；不能把本页当作完整开发教程。
