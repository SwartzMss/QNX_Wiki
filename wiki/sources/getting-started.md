# 来源笔记：Getting Started with the QNX OS

> 适用版本：QNX SDP 8.0 · 纳入日期：2026-11-27 · 范围：选定章节

[官方指南](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/about.html)是一份面向实时系统开发者的入门材料。本轮只核对了以下章节；本地文件在 `raw/qnx-sdp-8.0/getting-started/`。

| 已读章节 | 本轮提取的信息 | 关联 Wiki |
| --- | --- | --- |
| [Message handling](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_msg_Message_handling.html)、[The client](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_msg_Client.html) | `MsgSend()`、`MsgReceive()`、`MsgReply()` 的请求/回复顺序 | [消息传递](../topics/message-passing.md) |
| [Creating the channel](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_msg_Creating_channel.html)、[Establishing a connection](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_msg_Establishing_connection.html) | 服务端通道与客户端连接 ID 的基本关系 | [消息传递](../topics/message-passing.md) |
| [Kernel states](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_procs_Kernel_states.html)、[Scheduling rules](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_procs_Scheduling_rules.html) | 线程阻塞状态与基本调度规则 | [进程与线程](../topics/processes-threads.md) |
| [Scheduling policies](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_procs_Scheduling_policies.html) | FIFO、RR 与同优先级调度的入门说明 | [进程与线程](../topics/processes-threads.md) |

这些章节是教学性解释。涉及精确 API 参数、错误码或某个补丁版本的行为时，仍需再查对应 API 参考与 release notes。
