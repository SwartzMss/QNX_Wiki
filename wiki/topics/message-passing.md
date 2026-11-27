# 消息传递与 IPC

> 适用版本：QNX SDP 8.0 · 更新：2026-11-27 · 来源：[Getting Started](../sources/getting-started.md)、[System Architecture](../sources/system-architecture.md)

## 它在系统中的位置

QNX OS 将消息传递作为主要的进程间通信方式；系统还提供信号、共享内存、消息队列、管道等方式。官方架构指南把 `MsgSend()`、`MsgReceive()`、`MsgReply()` 组成的消息交互描述为同步且复制数据的机制。[官方：Interprocess Communication](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.sys_arch/topic/ipc.html)

## 一次请求如何往返

1. 服务端创建 channel；客户端连接到该 channel，得到 connection ID（`coid`）。[官方：Creating the channel](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_msg_Creating_channel.html)、[Establishing a connection](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_msg_Establishing_connection.html)
2. 客户端调用 `MsgSend()` 发出请求并等待。服务端调用 `MsgReceive()` 取得消息和 receive ID（`rcvid`）；`rcvid` 供服务端针对该请求回复。[官方：Message handling](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_msg_Message_handling.html)、[The receive ID](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_msg_rcvid.html)
3. 服务端处理请求后用 `MsgReply(rcvid, …)` 发送状态和可选的回复数据，客户端的 `MsgSend()` 才返回。回复中的状态传给客户端作为 `MsgSend()` 的返回状态。[官方：Replying to the client](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_msg_Replying_to_client.html)、[Message handling](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_msg_Message_handling.html)

“客户端在 `MsgSend()` 中阻塞”有两个阶段：等待服务端接收时是 **SEND**；服务端已接收、尚未回复时是 **REPLY**。服务端没有消息可收时可以处于 **RECEIVE**。这些是[线程状态](processes-threads.md)，不应混成一个笼统的“等待”。[官方：Kernel states](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_procs_Kernel_states.html)

## 与资源管理器的关系

[资源管理器](resource-managers.md)是使用系统消息机制提供服务的一种常见模式：客户端通过 `open()`、`read()`、`write()` 等接口访问路径，资源管理器接收相应请求。[官方：What Is a Resource Manager?](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.resmgr/topic/overview.html)

## 继续阅读

- [一次请求的状态变化](../questions/message-roundtrip.md)：把本页与线程状态页合起来回答一个实际问题。
- 待补：脉冲、超时、跨节点通信，以及 API 错误码；本页尚未核对这些细节。
