# Wiki 维护日志

> 范围：QNX SDP 8.0 · 更新：2026-11-27

## [2026-11-27] ingest | 首轮三个 QNX SDP 8.0 主题

- 来源：Getting Started、System Architecture、Writing a Resource Manager 的[已读章节清单](index.md#资料与问答)。
- 新建：消息传递、进程与线程、资源管理器三篇主题页；索引、版本范围及来源笔记。
- 范围限制：没有把已下载的全部 730 个 HTML 页面标为已纳入。
- 后续问题：补查消息超时/错误码、资源管理器权限与多线程细节，并逐项审阅 8.0.6 release notes 对相关主题的影响。

## [2026-11-27] query | `MsgSend()` 请求往返

- 问题：客户端发送请求后，SEND、REPLY 与服务端 RECEIVE 状态如何衔接？
- 结果：核对消息处理、线程状态和调度规则后，写入[问答页](questions/message-roundtrip.md)，并加入[索引](index.md)。

## [2026-11-27] lint | 首轮 Wiki 健康检查

- 检查范围：`wiki/` 的 10 个 Markdown 页面，以及其中的本地链接、版本标注和官方 URL。
- 结果：本地链接无断链；主题页和问答页均标明 SDP 8.0；引用的 23 个不同官方 URL 中，21 个可在 `raw/manifest.tsv` 找到。另 2 个是[版本范围页](versions.md)中注明的在线导航入口，没有作为本轮主题页的技术依据。
- 人工核对：三个主题页与已读章节笔记之间未发现相互矛盾的陈述；[索引](index.md)可到达本轮所有页面。此结论仅覆盖已读章节，不代表其余官方文档已完成审阅。
- 下一轮重点：对 8.0.6 release notes、消息超时与资源管理器权限做专题核对。
