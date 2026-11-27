# QNX LLM Wiki

基于 [Karpathy 的 LLM Wiki 思路](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)和 [Astro-Han/karpathy-llm-wiki](https://github.com/Astro-Han/karpathy-llm-wiki) Skill，为 QNX 官方文档逐步建立可维护的知识库。项目内的 [qnx-llm-wiki Skill](.agents/skills/qnx-llm-wiki/SKILL.md) 增加了首次使用的 Init，之后沿用上游的 **Ingest → Query → Lint** 工作流，并适配已有的 QNX HTML 原文。

## 什么是 LLM Wiki？

`raw/` 保存原始资料，LLM 在入库时把值得复用的知识编译到 `wiki/`。以后提问先检索知识页，答案链接回知识页；新增资料再更新相关页面、索引和交叉引用。人负责选择资料、提出问题并审阅结论。

| 操作 | 做什么 | 结果 |
| --- | --- | --- |
| **Ingest（入库）** | 阅读指定的原始资料，判断是新建、更新、存在争议还是没有新增内容 | 创建或更新知识页、索引与日志；没有新增内容时只记日志 |
| **Query（提问）** | 检索现有 Wiki 并综合回答 | 在对话中回答，引用知识页；默认不写文件 |
| **Lint（检查）** | 检查索引、链接、原文引用和可能的事实问题 | 修复安全的结构问题，报告需要判断的问题 |

“归档回答”是 **Query 的可选步骤**：只有明确要求保存时，才把这一次回答另存为快照页。它不是第四种入库操作，也不替代从官方原文编译知识页。

## 快速开始

在本仓库中打开 Codex 会话，直接指定 `qnx-llm-wiki` Skill。**这里安装的是 1 个 Skill；Init、Ingest、Query、Lint 是它内部的 4 种操作，不是 4 个独立的 Skill 或斜杠命令。** `wiki/` 已初始化，目前还没有入库的知识页。

### 0. Init：建立空 Wiki

首次使用时可以说：

> 使用 qnx-llm-wiki 初始化 wiki；只建立索引和日志，不入库原始资料。

Init 只创建缺失的 `wiki/index.md` 和 `wiki/log.md`，不会修改 `raw/` 或批量生成知识页。重复执行不会重置已有 Wiki。如果跳过这一步，首次 Ingest 也会先完成初始化。

### 1. Ingest：入库第一篇资料

> 使用 qnx-llm-wiki，入库 `raw/qnx-sdp-8.0/system-architecture/ipc_Sync_messaging.html`。围绕同步消息传递整理可复用的知识；核对 `raw/manifest.tsv` 中的官方 URL，标明适用的 QNX SDP 8.0 版本，更新 Wiki 索引和日志。

Skill 直接读取这篇 HTML，不会在 `raw/` 中生成 Markdown 副本。一份原文可以更新多篇知识页；多份原文也可以汇入同一篇知识页。建议每次指定一篇页面或一组范围明确的章节，而不是一次处理全部文档。

每次 Ingest 结束前，Skill 会自动检查本次新建或更新的知识页：核对原文链接、Wiki 链接、索引和可能不忠实于原文的表述，并报告仍需判断的问题。你不需要再手动运行一次本次入库的检查。

### 2. Query：向 Wiki 提问

> 使用 qnx-llm-wiki，根据当前 wiki 回答：QNX 的同步消息传递中，客户端和服务端分别会经历哪些阻塞状态？引用相关知识页。

Query 先查 `wiki/index.md`，再搜索知识页正文；普通提问只在对话中回答。空 Wiki 会提示尚无已入库知识。如果 Wiki 没有足够依据，可以指定相关官方 HTML 再要求 Ingest，而不是让答案依赖未入库的原文。

要把一个问题变成**可持续维护的知识**，可以一次说明问题、原文范围和入库要求：

> 使用 qnx-llm-wiki，回答“同步消息传递为什么会阻塞？”。先检查现有 wiki，再从 `raw/qnx-sdp-8.0/system-architecture/ipc_Sync_messaging.html` 入库有依据的新知识，更新相关知识页、索引和日志；最后引用知识页回答。

如果只想保存一次已回答问题的表述，明确要求归档：

> 使用 qnx-llm-wiki，把刚才基于 wiki 的回答归档为问答快照，保留知识页引用，并更新索引和日志。

归档页引用已有知识页，不直接代替有 `Raw` 引用的知识页；它是当时答案的快照，以后不会随源知识页自动改写。

### 3. Lint：检查整个 Wiki

> 使用 qnx-llm-wiki 检查当前 wiki 的索引、链接、原文引用和事实依据；修复安全的结构问题，列出需要人工判断的内容。

也可以单独运行机械证据检查：

```bash
python3 .agents/skills/qnx-llm-wiki/scripts/check_evidence.py .
```

完整 Lint 适合在积累一批知识页后按需运行，用于发现跨文章矛盾、过时内容和全局链接问题；每次 Ingest 已自动检查本次改动。脚本报告的是待核对线索，不会自动修改事实。刚开始建库时，未引用的原始页面会很多，这表示尚未入库的资料范围。

## 目录结构

```text
QNX_Wiki/
├── .agents/skills/qnx-llm-wiki/   # 工作流、模板与检查脚本
├── raw/                           # 不改写的官方原始资料
│   ├── manifest.tsv               # 本地文件、官方 URL 与 SHA-256
│   └── qnx-sdp-8.0/               # HTML 页面与图示
└── wiki/                          # Init 时创建
    ├── index.md                   # 全局知识页索引
    ├── log.md                     # 操作日志
    └── <topic>/<article>.md       # 按概念组织的知识页
```

Wiki 知识页使用 Skill 的 [文章模板](.agents/skills/qnx-llm-wiki/references/article-template.md)：标题、`Sources`、指向原始 HTML 的 `Raw`、`Updated`、正文及必要的交叉引用。技术结论还应链接到 `raw/manifest.tsv` 记录的具体官方页面。`wiki/` 不要求与 `raw/` 一一对应。

## 本项目与上游示例的差异

上游示例通常在 Ingest 时把新资料保存为 `raw/*.md`。本项目已经保存了 [QNX SDP 8.0 官方文档快照](raw/README.md)：730 个 HTML 页面和 124 张图示，文件与官方 URL 的对应关系见 [manifest.tsv](raw/manifest.tsv)。Skill 直接把现有 HTML 当作 Raw 引用，不批量转换、不修改原文。需要其他 QNX 版本的知识时，应先补充对应版本的官方来源，再标明版本入库。
