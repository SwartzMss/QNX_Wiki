# 资源管理器

> 适用版本：QNX SDP 8.0 · 更新：2026-11-27 · 来源：[Writing a Resource Manager](../sources/writing-resource-manager.md)、[System Architecture](../sources/system-architecture.md)

## 是什么

资源管理器是向文件系统命名空间注册路径名的进程。其他进程通过该路径与它交互；它既可代表真实设备，也可代表虚拟设备，并作为独立于内核的用户态进程运行。[官方：What Is a Resource Manager?](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.resmgr/topic/overview.html)、[System Architecture：Resource Managers](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.sys_arch/topic/resource.html)

## 路径如何变成服务

资源管理器使用 `resmgr_attach()` 注册自己负责的路径名前缀。例如官方示例注册 `/dev/sample`，客户端随后可通过该路径发起 `open()`。注册前要先建立相应的 dispatch、connect 与 I/O 处理配置。[官方：Put a name into the namespace](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.resmgr/topic/skeleton_RegisterName.html)

`open()` 建立客户端和资源管理器之间的上下文；后续 `read()` 对应的 `_IO_READ` 等 I/O 消息使用这个已有上下文，而不必每次重新传完整路径。资源管理器通过 `resmgr_io_funcs_t` 配置 I/O 处理函数，并可在默认处理函数基础上覆盖需要定制的操作。[官方：I/O messages](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.resmgr/topic/fleshing_out_IO_messages.html)

## 实现层次

官方指南把常见实现分为 `iofunc`、`resmgr`、`dispatch` 和可选的线程池层：`iofunc` 提供 POSIX 文件语义相关的默认处理与辅助函数；`resmgr` 分派资源管理器消息；`dispatch` 统一处理多类消息或脉冲；线程池允许多个工作线程处理请求。[官方：Layers in a resource manager](https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.resmgr/topic/skeleton_RESMGR_layers.html)

资源管理器使用的底层通信与[消息传递](message-passing.md)相连。本页只建立概念地图；编写可运行设备驱动还需要阅读完整指南中的权限、错误处理、同步和设备专用章节。
