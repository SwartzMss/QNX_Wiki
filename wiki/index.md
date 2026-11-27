# QNX Wiki

> 范围：QNX SDP 8.0 · 更新：2026-11-27 · 状态：首轮试点

这个 Wiki 从本仓库 `raw/` 中的 QNX 官方文档归纳知识。先阅读[版本范围](versions.md)，再按问题进入主题页。具体技术断言以页面中的官方章节链接为依据；[资料清单](../raw/README.md)列出本地快照。

## 核心主题

| 页面 | 能回答的问题 |
| --- | --- |
| [消息传递与 IPC](topics/message-passing.md) | 客户端和服务端如何完成一次请求与回复？ |
| [进程、线程与调度](topics/processes-threads.md) | QNX 调度什么？SEND、RECEIVE、REPLY 状态各表示什么？ |
| [资源管理器](topics/resource-managers.md) | 路径名如何连接到用户态服务？`open()` 后的 I/O 如何处理？ |

## 资料与问答

- [Getting Started：本轮纳入章节](sources/getting-started.md)
- [System Architecture：本轮纳入章节](sources/system-architecture.md)
- [Writing a Resource Manager：本轮纳入章节](sources/writing-resource-manager.md)
- [问答示例：一次 MsgSend 请求的状态变化](questions/message-roundtrip.md)
- [维护日志](log.md)

## 尚未纳入

当前只完成上述三个主题的首轮整理。`raw/` 中的 Quickstart、Programmer's Guide 和 8.0.6 Release Notes 尚未系统纳入主题页；网络、BSP、启动镜像、调试等主题也尚未建立。不要将“已下载”理解为“已整理”。
