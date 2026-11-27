# QNX 官方原始资料

`raw/` 保存官方资料的本地快照，供后续 Wiki 阅读、归纳和核对。原始 HTML 不在这里改写；Wiki 页面应另放在 `wiki/`，并链接回下表中的官方页面。

## SDP 8.0 文档快照

抓取日期：2026-11-27。HTML 文件以官网原始文件名保存；本地目录中的 `foo.html` 对应官方地址中同名的 `topic/foo.html`。

| 本地目录 | 官方文档 | HTML 页 | 图示 | 官方入口 |
| --- | --- | ---: | ---: | --- |
| `qnx-sdp-8.0/quickstart/` | Quickstart Guide | 5 | 2 | https://www.qnx.com/developers/docs/8.0/com.qnx.doc.qnxsdp.quickstart/topic/about.html |
| `qnx-sdp-8.0/getting-started/` | Getting Started with the QNX OS | 227 | 40 | https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.getting_started/topic/about.html |
| `qnx-sdp-8.0/system-architecture/` | System Architecture | 179 | 49 | https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.sys_arch/topic/about.html |
| `qnx-sdp-8.0/programmers-guide/` | Programmer's Guide | 218 | 26 | https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.prog/topic/about.html |
| `qnx-sdp-8.0/resource-managers/` | Writing a Resource Manager | 100 | 7 | https://www.qnx.com/developers/docs/8.0/com.qnx.doc.neutrino.resmgr/topic/about.html |
| `qnx-sdp-8.0/release-notes-8.0.6.html` | QNX SDP 8.0.6 Release Notes | 1 | — | https://www.qnx.com/developers/docs/relnotes8.0/com.qnx.doc.release_notes/topic/8.0.6_prod_release_notes/8.0.6_rn.html |

正文和正文引用的图示均已保存，共 730 个 HTML 页面、124 张图示。HTML 保持官网原文，图示放在各指南的 `images/` 子目录。这些是本地阅读副本，不提交到 Git。具体技术结论应标明适用的 SDP 版本，并引用官方文档中的具体页面。官方文档可能继续更新，快照内容以抓取日期为准。

`manifest.tsv` 列出全部 854 个本地文件的路径、官方 URL、字节数和 SHA-256。清单可以提交到仓库，用于核对本地快照；官方原文仍只保存在本地。
