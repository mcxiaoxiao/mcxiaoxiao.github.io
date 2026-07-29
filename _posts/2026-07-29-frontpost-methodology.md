---
title: 'FrontPost 的方法论'
date: 2026-07-29
permalink: /posts/frontpost-methodology
redirect_from:
  - /posts/frontpost-auto-editorial
tags:
  - FrontPost
  - AI
  - 技术
  - 软件工程
---

FrontPost 是面向科研与前沿资讯的持续追踪和可信交付系统。搜索、AI 问答、论文阅读、公开邮报和个人推荐不是独立产品，而是同一套研究证据系统的不同入口与输出。

## 系统目标

FrontPost 接受三类用户意图：

- 浏览和订阅公开领域邮报。
- 围绕研究问题搜索、阅读、比较和追问。
- 创建长期 Track，持续监控主题、作者、机构、方法、数据集或结论变化。

系统遵循五条原则：

1. 同一研究维护一个规范 `Work`，来源和版本独立保留。
2. 回答和简报中的结论必须关联可检查的证据。
3. 公开版与个人版共用来源、证据、运行时和交付基础设施。
4. 同一份邮报先冻结一个 `Edition revision`，渠道只做格式适配。
5. AI 负责规模化处理，公开发布和高风险结论保留人工责任点。

## 系统架构

![FrontPost 系统总架构](/images/2026/frontpost-methodology/frontpost-system-architecture.svg)

系统分为七层：

| 层 | 主要组件 | 责任 |
| --- | --- | --- |
| 用户界面 | Web、Expo、Reader、Ask、Track、编辑控制台 | 输入、阅读、交互和审批 |
| 产品服务 | Better Auth、FastAPI | 身份、权限、领域 API 和业务幂等 |
| 统一 AI | DeepAgent、LangGraph、Skills、Tools | 长任务编排、上下文、工具调用和恢复 |
| 交互运行时 | assistant-ui | 展示消息、证据、工具状态和人工中断 |
| 异步执行 | TaskIQ、Redis | 抓取、解析、批处理和渠道投递 |
| 内容生产 | 来源、归一、证据、排序、成稿、审核、发布 | 生成 Work、Evidence 和 Edition |
| 数据层 | PostgreSQL、对象存储 | 保存业务状态、长期工件和不可变版本 |

TaskIQ 是执行池，不是第二个 Agent 编排器。assistant-ui 是交互层，不是事实存储。DeepAgent 统一管理 Ask、Track 和 Edition 的运行，但例行内容生产使用固定、版本化的 Skill runbook。

## 运行闭环

![FrontPost 用户与内容运行闭环](/images/2026/frontpost-methodology/frontpost-operating-loop.svg)

系统有两种主要输出：

- `Public Edition`：按公开领域策略生成，经过编辑审批后发布。
- `Personal Track / Edition`：围绕用户研究任务运行，只在出现有效新增、版本变化、反证或趋势拐点时通知。

二者共用一条基础链路：

```text
用户意图或定时触发
  → 多源发现
  → Work 归一与版本关联
  → Evidence 补全与比较
  → 规则和 AI 处理
  → 校验与人工责任点
  → 冻结 Edition
  → Web / Email / RSS / MCP / Zotero
  → 阅读和编辑反馈
```

RSS / Atom 用于增量发现，搜索 API 用于回看和元数据补全，Hugging Face Daily / Trending 与 GitHub 只提供有限权重的社区信号。技术事实仍回到论文、正式出版元数据和合法来源。

## 核心对象

![FrontPost 核心领域对象](/images/2026/frontpost-methodology/frontpost-core-objects.svg)

核心领域对象包括：

- `Track`：研究任务、范围、排除项、来源、频率和证据阈值。
- `Run`：一次可恢复执行及其 Skill checkpoints。
- `Work / Version`：研究实体及预印本、正式版、勘误和撤稿关系。
- `Evidence / CitationTrace`：结论到证据、版本和原始来源的路径。
- `Edition / EditionItem`：公开或个性化内容及不可变 revision。
- `Delivery`：某个 Edition 在某渠道上的幂等投递记录。
- `Feedback`：阅读行为、编辑修改和系统质量指标。

页面、提示词和渠道不单独维护事实状态，全部围绕这些领域对象工作。

## AI 与工程边界

确定性工作优先由代码完成，包括抓取、解析、标识符归一、哈希、schema 校验和幂等发布。模型用于语义匹配、差异判断、排序辅助、成稿和证据一致性检查。

每个 Skill 只传递结构化 Artifact，记录输入输出 URI、内容哈希、Skill / policy / model 版本、耗时、成本和告警，不保存或依赖模型思维链。失败后从当前 checkpoint 恢复。

公开发布、高风险内容、低置信 Work 合并、来源冲突和许可不明必须进入人工审核。

## 存储与一致交付

PostgreSQL 保存身份、Profile、Track、权限、订阅和查询索引。对象存储保存来源快照、Work versions、Evidence、Run checkpoints、Edition revisions 和渠道 manifests。Redis 只承担队列、短期锁、限流和可丢失缓存。

`publish-edition` 先冻结 Edition，`deliver-edition` 再生成渠道视图。Email、RSS、MCP、Zotero 和个人推荐首页不得重新排序、重新调用模型或改写事实。

## 实施顺序

1. 稳定现有内容模型、测试和前端性能基线。
2. 建立 Track、Work、Evidence、Run、Edition、Delivery 契约。
3. 接入 arXiv、Hugging Face、OpenAlex、Crossref，跑通采集和归一。
4. 接入 DeepAgent 与 assistant-ui，跑通 Ask、Track 和人工审批。
5. 生成内部 Edition，再依次上线 Web、RSS、Email、MCP 和 Zotero。
6. 接入阅读反馈和版本化评测，逐步开放个性化排序。

第一阶段的完成标准是：一个公开领域连续稳定运行；单源失败可降级；每个结论可追溯；运行可恢复；重跑不重复投递；所有渠道指向同一 Edition revision。

参考实现与资料：[Deep Agents](https://docs.langchain.com/oss/python/deepagents/overview)、[assistant-ui](https://www.assistant-ui.com/docs/runtimes/langchain)、[dailypaper-skills](https://github.com/huangkiki/dailypaper-skills)、[paper-daily](https://github.com/Futuresxy/paper-daily)、[AI 论文简报方法论](https://ai-brief.liziran.com/zh/methodology)、[arXiv API / RSS](https://info.arxiv.org/help/api/index.html)、[Hugging Face Daily Papers](https://huggingface.co/papers)、[OpenAlex](https://developers.openalex.org/) 和 [Crossref](https://www.crossref.org/documentation/retrieve-metadata/rest-api/)。
