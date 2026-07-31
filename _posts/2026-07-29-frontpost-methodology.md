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

FrontPost 持续完成来源发现、研究归一、质量评估、内容生成、个性化筛选和多渠道投递。搜索、AI 问答、论文阅读、公开邮报和个人推荐共用 Work、Markdown、Conversation、Run 与 Delivery 模型。

## 内容如何产生

FrontPost 每天围绕六个公开大领域和二十二个细分领域生成两类内容：

- `News`：研究动态、产品发布、数据集、标准、政策及其他可核实的新变化。
- `Paper`：围绕单篇或一组论文生成的研究解读。

每份内容保存为独立 `ContentDocument` Markdown。一次整理可以生成多份 Markdown，并由一个 `Edition` manifest 记录顺序、摘要和内容哈希。Web、Email、RSS、MCP 和 Zotero 从同一组 Markdown 与 manifest 渲染。

公共内容按日期、领域、细分领域和语言组织：

```text
public/{date}/{field}/{subfield}/{document_id}/{locale}.md
```

同一个内容 ID 分别保存中文、英文、阿拉伯文等语言文件。产品展示滚动最近七天目录，底层文件继续按自然日期归档。

![FrontPost 系统总架构](/images/2026/frontpost-methodology/frontpost-system-architecture.svg)

## 公共内容与个人内容

公共自动任务持续维护六个领域的内容池。每个细分领域分别生成 News 和 Paper。公共首页在最近七天范围内按全局质量选择内容，并应用领域配额、Work 去重和来源多样性约束。首页不计算个人相关性。

个人任务先检索最近七天公共内容，再根据用户显式编写的 Focus 执行专项搜索。系统合并两组候选、去重和排序，对公共目录尚未覆盖的候选生成个人 Markdown，并用公共高质量内容补齐用户要求的 News/Paper 数量。

公共 Markdown 进入个人工作区时保持内容哈希不变。个人 Edition 通过内容地址引用完成虚拟复制；导出工作区时再生成文件副本。公共内容不根据个人阅读历史或收藏改写。

个人任务发现具有公共价值的新 Work 时，公共 Worker 会移除 Focus、Conversation memory、收藏和阅读历史等用户上下文，再按公共模板生成多语言内容。

![FrontPost 公共与个人内容运行](/images/2026/frontpost-methodology/frontpost-operating-loop.svg)

## 如何判断研究质量

候选进入评分前必须有可解析的原始来源和规范 URL，主要陈述必须关联来源片段、公开数据或明确方法。来源许可、Work 归一或证据无法满足门槛时，候选退出生产链路。

质量评估保存分项向量、0–100 汇总分、逐项依据、rubric 版本和模型/代码版本。它服务于候选筛选、算力分配和排序，不构成论文质量真值。

Paper 的质量维度为：

| 维度 | 分值 |
| --- | ---: |
| 研究重要性 | 20 |
| 方法严谨性 | 25 |
| 证据充分性 | 25 |
| 可复现性与透明度 | 15 |
| 新颖性与信息增量 | 15 |

News 的质量维度为：

| 维度 | 分值 |
| --- | ---: |
| 一手来源程度 | 25 |
| 交叉验证 | 20 |
| 事实完整性 | 20 |
| 有效信息增量 | 20 |
| 潜在影响 | 15 |

期刊名次、引用数、机构声誉、社交热度和 GitHub star 只能作为有上限的辅助信号。全局质量只计算一次并缓存，供公共首页和所有个人任务复用。

个人相关性按以下顺序取信号：用户显式 Focus、当前 Conversation 的任务上下文与长期记忆、收藏和阅读反馈、通用主题偏好。个人排序公式为：

```text
personal_score = 0.65 × relevance_score + 0.35 × quality_score
```

个性化候选不足时，系统按 quality_score 从公共池补齐内容。

评分设计参考 [NIH 对重要性、严谨性与可行性的定义](https://www.grants.nih.gov/policy-and-compliance/policy-topics/peer-review/simplifying-review/framework)、[PLOS 可复现性共识](https://journals.plos.org/plosbiology/article?id=10.1371%2Fjournal.pbio.3003726)、[TOP 透明度规范](https://www.cos.io/initiatives/top-guidelines)和 [DORA 指标使用原则](https://sfdora.org/resource/guidance-on-the-responsible-use-of-quantitative-indicators-in-research-assessment/)。模型评分还需考虑表面改写带来的操纵风险，相关实验见 [Gaming AI-Assisted Peer Reviews Poses New Risks](https://arxiv.org/abs/2606.10159)。应当建立一套透明安全机制：限制 AI 单独给出最终打分，强化全文综合校验而非仅依赖摘要，并对好的文笔或好的"故事"持无视甚至降权的态度。

## Conversation、Run 与自动投递

`Conversation` 保存连续消息、记忆摘要、关联 Focus 和多个 Run。用户提问、继续追问、点击“立即整理”和定时任务都在 Conversation 下创建 Run。

每个自动 Focus 关联一个长期 Conversation。每次定时触发创建独立 Run，记录输入快照、Skill 版本、checkpoint、成本、输出 URI 和状态。Skill context 依次装配 Focus、Conversation memory、当前触发参数、阅读反馈摘要、公共候选和外部来源。显式 Focus 拥有最高优先级。

PostgreSQL 保存时区、目标发送时间、频率、提前量、下一次触发时间和幂等键。Scheduler 使用数据库行锁抢占到期规则，按 `schedule_id + scheduled_for` 创建唯一 Run，再把任务写入 Redis。

生成任务根据历史耗时提前启动。同一发送时间的大批任务按稳定散列分布到提前窗口。临近截止时间时，Run 停止扩大搜索范围，使用已验证结果和公共内容补齐。Edition manifest 完成后才创建 Delivery。

初始 Worker 分为两个独立队列：

- `interactive`：至少五个副本，处理实时提问和立即整理。
- `scheduled`：至少五个副本，处理公共采编、个人定时整理和投递。

## 四层架构

| 层 | 技术构件 | 责任 |
| --- | --- | --- |
| 产品与交付 | Web、Expo、Reader、Search、Ask、Email、RSS、MCP、Zotero | 用户输入、内容呈现与渠道适配 |
| 产品服务与任务编排 | Better Auth、FastAPI、assistant-ui、DeepAgent/LangGraph、Skills、Scheduler、TaskIQ | 身份、领域 API、Conversation/Run 和任务执行 |
| 内容生产 | 来源、Work 归一、质量评估、公共池、个人检索、Markdown、Edition | 生成和复用 News/Paper |
| 数据基础设施 | PostgreSQL、对象存储、Redis | 持久状态、内容工件与 Worker 队列 |

PostgreSQL 保存业务状态、Conversation/Run、定时规则、评分索引、Edition 索引和 Delivery。对象存储保存来源快照、Markdown、运行工件和 manifest。Redis 主要提供 TaskIQ 队列、任务结果、短锁和临时缓存。

## 八个核心对象

![FrontPost 核心领域对象](/images/2026/frontpost-methodology/frontpost-core-objects.svg)

- `User / Profile`：身份、语言、权限、阅读和投递偏好。
- `Focus`：研究范围、排除项、数量、语言、时序和渠道配置。
- `Conversation`：连续消息、长期记忆和多个 Run 的上下文容器。
- `Run`：一次交互、立即整理或定时执行及其状态与工件。
- `Work`：规范研究或新闻实体、来源、版本和全局质量评估。
- `ContentDocument`：一份原子 News/Paper Markdown 及语言变体。
- `Edition`：一次 Run 产生的文档清单、顺序和摘要 manifest。
- `Delivery`：接收者、渠道、状态、幂等键和实际内容哈希。

```text
User → Focus → Conversation → Run
Run → Work → ContentDocument
Run → Edition ↔ ContentDocument
Edition → Delivery
```

来源记录、引用轨迹、质量评估、Schedule 和反馈事件作为上述对象的结构化子记录或运行数据保存。

## 一致交付

Run 完成内容整理后直接写入 Edition manifest。重新整理会产生新的 Run 和 Edition ID。历史 Delivery 继续引用原 manifest。

Delivery 使用 `edition_id + recipient_or_feed + channel` 作为幂等键。渠道只渲染 manifest 指向的 Markdown，不重新排序或生成内容。阅读、收藏、完成、跳过、不相关和退订用于下一次相关性排序与 Conversation memory，不修改已经发送的 ContentDocument。

完整架构文档维护在 [FrontPost 仓库](https://github.com/Huxun-Inc/frontpost)。
