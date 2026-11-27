# 进程、线程与调度

> 适用版本：QNX SDP 8.0 · 更新：2026-11-27 · 来源：[Getting Started](../sources/getting-started.md)、[System Architecture](../sources/system-architecture.md)

## 执行单位与地址空间

QNX OS 中，线程是执行和调度单位；进程为其线程提供地址空间，一个进程至少包含一个线程。同一进程中的线程共享该进程的地址空间，因此访问共享数据时要考虑同步。[官方：Threads and processes](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.sys_arch/topic/kernel_THREADSANDPRO.html)、[Back to processes and threads](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_procs_Processes_and_threads.html)

## 与消息传递相关的状态

| 状态 | 含义 |
| --- | --- |
| `STATE_SEND` | 客户端等待服务端接收消息。 |
| `STATE_RECEIVE` | 服务端等待客户端发送消息。 |
| `STATE_REPLY` | 客户端等待服务端回复。 |
| `STATE_READY` | 已可运行，但当前没有占用 CPU。 |
| `STATE_RUNNING` | 正在 CPU 上运行。 |

以上名称和含义来自官方[线程状态表](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_procs_Kernel_states.html)。阻塞的线程不消耗 CPU 执行时间；具体请求过程见[消息传递](message-passing.md)。

## 基本调度规则

在单 CPU 的入门模型中，同一时刻只有一个线程运行，内核选择最高优先级的就绪线程。FIFO 与 Round Robin（RR）主要规定**同优先级**线程如何轮换；RR 线程到达时间片后会触发重新调度。SMP 下多个 CPU 可同时运行线程，决策还要考虑线程可运行的 CPU 集合。[官方：Scheduling rules](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_procs_Scheduling_rules.html)、[Scheduling policies](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/s1_procs_Scheduling_policies.html)

本页是理解请求阻塞的基础，不涵盖完整的优先级继承、处理器亲和性或全部调度策略。
