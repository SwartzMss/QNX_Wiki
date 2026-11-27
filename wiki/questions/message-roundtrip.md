# 问答：一次 `MsgSend()` 请求中线程如何变化？

> 适用版本：QNX SDP 8.0 · 整理日期：2026-11-27 · 状态：已按官方章节核对

**问题：**客户端调用 `MsgSend()` 后，直到收到服务端回复，双方线程分别会发生什么？

**回答：**服务端先建立 channel，客户端连接后通过 `MsgSend()` 发送请求。若服务端尚未接收，客户端处于 `STATE_SEND`；服务端在等待请求时可处于 `STATE_RECEIVE`。服务端的 `MsgReceive()` 取得请求和 `rcvid` 后，客户端转为等待回复的 `STATE_REPLY`。服务端用 `MsgReply(rcvid, …)` 回复后，客户端解除等待；它是否立刻运行仍由调度决定，不能把“解除阻塞”直接等同于“立刻占用 CPU”。[官方：Message handling](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_msg_Message_handling.html)、[Kernel states](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_procs_Kernel_states.html)、[Scheduling rules](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_procs_Scheduling_rules.html)

**为什么存入 Wiki：**这个答案把[消息收发流程](../topics/message-passing.md)与[线程状态及调度](../topics/processes-threads.md)连接起来，后续分析请求延迟或服务端卡住时可以从这里开始。它没有覆盖超时、错误返回和脉冲；这些边界情况需要另查官方 API 文档。
