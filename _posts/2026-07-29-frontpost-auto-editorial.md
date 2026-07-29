---
title: 'FrontPost 的自动采编系统：用 DeepAgent 和 Skills 把一份邮报真正送出去'
date: 2026-07-29
permalink: /posts/frontpost-auto-editorial
tags:
  - FrontPost
  - AI
  - 技术
  - 软件工程
---

[上一篇 FrontPost 文章](/posts/frontpost-first-product)还在回答“为什么想做一份 AI 邮报”。这段时间继续往下做，我遇到了一个更具体的问题：页面可以先画得很好看，但每天的内容究竟从哪里来，怎么筛，怎么确认没有胡说，又怎么稳定送到每个人面前？

如果这些问题没有工程答案，FrontPost 就只是一套邮报界面。

我因此把最近的工作从继续扩页面，转向自动采编主链路。也研究了几套每日论文工作流和产品方法论，包括 `dailypaper-skills` 的一句话总入口、`paper-daily` 的定时采集与增量缓存，以及《AI 论文简报》公开的多信号筛选和编辑原则。[^1]

这次得到的核心结论很简单：**FrontPost 不应该是一条越来越长的提示词，也不应该依赖一次 Agent 会话把所有事情做完。它需要的是一组可以例行执行、单独重跑、能够审计的 Skills。**

## 先说现在做到哪了

FrontPost 已经有了比较完整的 Web 产品外壳：响应式页面、中文/英文/阿拉伯语、RTL、搜索、阅读器、订阅、账户和 AI 界面都能运行。FastAPI 也已经接入了 JWT/JWKS 验证、限流、安全中间件和 Semantic Scholar 搜索，TaskIQ、Redis、对象存储接口与部署骨架也有了位置。

但真正的自动采编还没有完成。当前 worker 里的采集和摘要任务仍然只是骨架，页面中的很多内容也还是本地 Markdown 或前端样例。

这其实是一个很好的停顿点：产品形态已经足够清楚，可以开始让后端围绕真实闭环生长，而不是继续堆功能清单。

我给第一阶段定下的目标只有一条：

> 先让一个公开领域稳定完成“采集 → 去重 → 评分 → 成稿 → 人审 → 冻结 Edition → 五端送达 → 反馈回收”。

## DeepAgent 是统一运行时，Skill 才是工作边界

FrontPost 的统一 AI 会使用 DeepAgent，Web 交互层继续使用 assistant-ui。DeepAgent 负责工具调用、上下文管理、持久执行和人工中断；assistant-ui 负责把消息、工具状态、证据、错误和审批动作清楚地呈现出来。Deep Agents 本身建立在 LangGraph 的持久化运行能力上，assistant-ui 也提供了直接连接 LangGraph / LangChain runtime 的适配器。[^2]

但我不准备让每天的采编变成一条开放式 CoT 链路。

日更任务的步骤应该固定下来。DeepAgent 调用总入口 `frontpost.daily-editorial`，总入口再执行一组版本化子 Skill。Agent 可以在成稿和证据复核时做语义判断，但不能临场跳过审核、扩大来源范围或改变发布规则。

![FrontPost 自动采编运行架构](/images/2026/frontpost-editorial/auto-editorial-runtime.svg)

这里还有一个容易混淆的边界：TaskIQ 不是第二个 Agent 编排器。它只是 DeepAgent 工具背后的异步执行池，负责抓取、解析、批量富化和渠道投递等长任务。完成后返回对象工件 URI，流程状态仍由 DeepAgent / LangGraph 和 run manifest 管理。

这样的好处是，智能判断和工程执行不会纠缠在一起：

- 抓取、哈希、格式解析、稳定标识符去重和 schema 校验尽量由代码完成。
- 语义相关性、论文差异、简报草稿和证据一致性可以使用模型。
- 每个 Skill 都有输入、输出、版本、状态、统计和 warning。
- 失败后从当前 Skill 重跑，不必把前面的内容再塞回一次大模型上下文。
- 系统保存评分理由、引用、模型版本和内容哈希，不保存也不依赖模型思维链。

## 一次例行采编到底怎么跑

目前规划的总流程有九个 Skill：

1. `collect-sources`：从 RSS、API 和趋势榜单并行采集。
2. `normalize-works`：统一 DOI、PMID、PMCID、arXiv ID，关联预印本和正式版本。
3. `rank-candidates`：规则评分为主，语义模型辅助，并加入版面多样性。
4. `enrich-evidence`：只给排名靠前的候选补摘要、开放全文、代码、数据和引用。
5. `draft-edition`：基于已登记证据生成 FrontBrief。
6. `verify-edition`：检查 schema、证据覆盖、许可证、重复和高风险表达。
7. `approve-edition`：编辑通过、修订或退回。
8. `publish-edition`：冻结不可变 revision，生成 Web 和归档产物。
9. `deliver-edition`：把同一份内容送到所有渠道。

![FrontPost Skill 式采编流程](/images/2026/frontpost-editorial/auto-editorial-skill-flow.svg)

参考 `dailypaper-skills` 时，我很喜欢“一句话总入口 + 子 Skill 可以单独调试”的设计。它让普通用户不必记住抓取、点评和笔记三条命令，同时保留了工程调试入口。[^3]

FrontPost 会沿用这个思想，但不会用 `/tmp` JSON 作为生产环境的步骤交接。每个 Skill 结束后都会写一个持久化 checkpoint，记录输入输出对象、内容哈希、Skill / policy / model 版本、耗时、成本、统计和告警。

任何来源出现 429 或 5xx 时，只让该来源进入退避，不拖垮整期；如果所有来源都失败，系统保留上一版并告警，绝不发布一份空邮报。证据不足的内容进入 `needs_review`，不能让模型仅凭标题猜测论文贡献。

## RSS、搜索 API 和 Trending 不是三选一

论文发现里很容易陷入“到底用 API 还是 RSS”的争论。我的答案是都用，但职责不同。

RSS / Atom 适合每天低成本发现新增。arXiv 为活跃分类提供每日更新的 RSS 和 Atom feed；搜索 API 更适合关键词查询、回看时间窗和补齐结构化元数据。[^4]

Hugging Face Daily Papers 和 Trending、GitHub 仓库活跃度则属于社区信号。它们能帮助发现哪些工作正在被讨论、有没有代码，但不能成为技术事实的唯一来源。[^5]

第一批来源准备这样分工：

- arXiv RSS / Atom：每日增量发现。
- arXiv API：关键词搜索、回看与版本信息。
- Hugging Face Daily / Trending：社区推荐和热度。
- OpenAlex：work、作者、机构、主题、引用和跨来源关系。
- Crossref：DOI、正式出版、勘误和撤稿元数据。
- 期刊、会议和实验室官方 RSS：直接发现来源方更新。
- 后续再接 Europe PMC、PubMed / PMC、Unpaywall、DOAJ 和更多合法开放全文来源。

`paper-daily` 给我的另一个提醒是：来源必须彼此隔离。它允许单一 feed 失败后继续采集其它来源，也会保留已有数据避免页面被清空，并用增量缓存减少重复工作。[^6] FrontPost 会把这种做法迁移到持久化 run manifest 和来源游标上。

## 对象存储就够了

自动采编会产生很多适合文件化保存的内容：原始响应、规范化 work、正文快照、评分结果、证据、草稿、审核记录、Edition 和各渠道投递结果。

这些工件没有必要一开始全部拆成关系表。FrontPost 会让 DeepAgent 的文件后端映射到对象存储，使用接近下面的目录语义：

```text
raw/{source}/{date}/{raw_hash}.json
works/{work_id}/manifest.json
runs/{run_id}/manifest.json
runs/{run_id}/skills/{step}-{skill}/{attempt}.json
editions/{edition_id}/{revision}/edition.json
editions/{edition_id}/{revision}/content/{locale}.md
editions/{edition_id}/{revision}/channels/{channel}/manifest.json
feedback/{date}/{event_id}.json
```

![FrontPost 内容与证据溯源](/images/2026/frontpost-editorial/auto-editorial-provenance.svg)

对象不可原地覆盖，内容变化就产生新的 hash 或 revision。最终简报里的每条事实都应该沿着 `Edition item → citation trace → content version → raw source snapshot → 原始链接` 找回去。

PostgreSQL 仍然适合 Better Auth 用户、Focus 配置、权限和需要低延迟查询的索引，但不必成为采编流水线每一步的事实数据库。Redis 也只负责队列和短期锁。

## 五个渠道，只允许一份内容

FrontPost 的推送面包括：

- 邮件
- MCP
- Zotero
- RSS
- 个人推荐首页

这里我做了一个很强的限制：**渠道不能各自生成内容，也不能各自重新排序。**

公开邮报先冻结 Public Edition；个性化场景先根据 `personal_score` 生成该用户唯一的 Personal Edition。之后五个渠道只负责格式适配，全部指向同一 item 顺序和 revision。

邮件可以渲染 HTML，MCP 可以把 Edition 和 citation trace 暴露为只读 resource，Zotero 可以同步论文和 FrontPost note，RSS 输出订阅 feed，个人推荐首页负责日常阅读。但它们不能分别叫一次模型，再得到五种略有差异的结论。

公开 Web 文章页、Markdown 归档和 sitemap 属于发布产物，不单独算一个推送渠道。

## 评分不能只看热度

公开版和个人版会使用两段分数：

```text
editorial_score =
  主题相关性
  + 证据与来源质量
  + 相比近期内容的有效新增
  + 时效
  + 工程可用性
  + 有上限的社区趋势信号
  - 风险惩罚

personal_score = editorial_score + Focus 匹配 + 阅读反馈
```

机构名字、HF upvote、GitHub star 都可以是信号，但不能单独决定入选。否则系统很容易永远追着少数机构和热门方向跑，把小众但真正有价值的工作全部漏掉。

算法负责收窄范围，编辑负责最终判断。公开推送的第一版仍然必须经过人审。每篇简报固定回答问题、变化、意义、证据边界和原始来源，不确定就明确写出来。

## 用数据改 Skill，不凭感觉改总模型

我不想让“优化 AI”最后变成不断换一个更贵的模型。更实际的做法是分别看每个环节出了什么问题：

- 来源成功率、429、延迟和字段缺失，决定要不要调限速或增加替代源。
- 标识符覆盖、误合并和漏合并样本，改进去重规则。
- 编辑接受率、退回原因和领域覆盖，调整评分与版面策略。
- 引用覆盖、事实修正和编辑改动类型，改进成稿模板。
- 五个渠道的成功率、重复投递和版本一致率，修复渠道适配。
- 读完、收藏、追问、不相关和退订，校准个人推荐；不能只看点击率。

![FrontPost 采编反馈与优化闭环](/images/2026/frontpost-editorial/auto-editorial-feedback.svg)

节奏也不需要复杂：每天看来源与投递健康，每周抽样一批候选复核入选和落选，每月版本化调整一次规则或模板。先在固定历史时间窗回放，再生成内部 Edition 对比，最后只灰度一个领域；效果变差就回滚版本，不修改历史邮报。

## 接下来怎么做

我把实施顺序压成四段：

1. 稳定当前内容模型和测试基线。
2. 先接 arXiv、HF、OpenAlex、Crossref，跑通对象存储中的采集、归一和去重。
3. 生成内部 Edition，在 assistant-ui 里完成证据查看和人工审批。
4. 先上线个人推荐首页、RSS 和邮件，再接 MCP 与 Zotero。

完成的标准也不是“网页显示了几篇论文”，而是定时和手动运行走同一入口、单源失败不清空内容、每篇入选都有来源和评分理由、失败能从当前节点重跑、五端内容一致、重跑不会重复发邮件。

FrontPost 仍然离这个目标有不少工程工作。但现在至少不只是一个“AI 自动找内容”的模糊想法了。它已经有了一条可以逐步实现、每一步都能验证的路。

[^1]: 主要参考 [dailypaper-skills](https://github.com/huangkiki/dailypaper-skills)、[paper-daily](https://github.com/Futuresxy/paper-daily) 和李自然的 [《AI 论文简报：方法论》](https://ai-brief.liziran.com/zh/methodology)。这些项目分别提供了 Skill 编排、定时增量工作流和编辑筛选方面的具体启发。
[^2]: [Deep Agents 官方文档](https://docs.langchain.com/oss/python/deepagents/overview)将其定义为建立在 LangGraph 之上的 agent harness，提供 planning、文件后端、子 Agent、持久记忆和 human-in-the-loop；[assistant-ui LangChain Runtime](https://www.assistant-ui.com/docs/runtimes/langchain)则可以读取 LangGraph 自定义状态、工具调用、子任务和 interrupts。FrontPost 会使用这些执行与交互能力，但日更流程遵循固定 Skill runbook。
[^3]: `dailypaper-skills` 的 [架构说明](https://github.com/huangkiki/dailypaper-skills/blob/main/ARCHITECTURE.md)把每日推荐拆成 fetch、review、notes 三步，并由 `daily-papers` 作为用户总入口。FrontPost 借鉴入口与子 Skill 边界，不沿用个人 Obsidian 和 `/tmp` 文件作为生产存储。
[^4]: [arXiv API](https://info.arxiv.org/help/api/index.html)提供公开 API；[arXiv RSS / Atom 文档](https://info.arxiv.org/help/rss.html)说明活跃学科分类提供每日更新的 RSS 2.0 和 Atom feed。实际使用还需要遵守其 API 条款、限速和品牌说明。
[^5]: [Hugging Face Daily Papers](https://huggingface.co/papers)和 [Trending Papers](https://huggingface.co/papers/trending)提供日、周、月社区推荐与热度视角。这些信息适合做关注度信号，论文结论仍以原文和可靠元数据来源为准。
[^6]: `paper-daily` 的 [GitHub Actions 工作流](https://github.com/Futuresxy/paper-daily/blob/main/.github/workflows/paper-daily.yml)同时支持定时、手动和配置变更触发，并通过缓存保持增量数据；其采集器还允许单一来源失败后继续运行。FrontPost 会把同类恢复语义放进对象存储 manifest 和来源游标。
