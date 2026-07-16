---
title: '我想做一个 AI 邮报（FrontPost）'
date: 2026-07-15
permalink: /posts/frontpost-first-product
tags:
  - 产品
  - AI
  - 技术
  - 日常
---

最近我又有了一个小想法，而且很想把它做出来。

[上上篇](/posts/llmapi-gateway)还在想 LLM API Gateway，也认真研究了多模型接入、任务状态、成本和合规。但它涉及的基础设施、企业需求和治理边界太多，对我现在想做的试水项目还是重了一点。

这次的想法叫 FrontPost。它离我的日常更近，也让我产生了一种“这个我真的很想用，所以很想尽快做出来”的冲动。

为什么我会想要这个东西
======

因为这基本就是我自己的痛点。

我并不喜欢又臭又长的 paper。确认它值得读之前，我不想先花半天从头啃到尾。我可能就是那种专注度不太够、又什么都想知道一点的人。我现在只是个普通计算机学生，关心前沿也不需要先给自己贴上 AI researcher 的标签。能不能天天读论文、熟不熟悉所有术语，都不该决定一个人有没有资格知道最近发生了什么。

这种害怕其实很朴素：几个月没看，怎么感觉整个世界又换了一套说法？

考研这一年尤其明显。一边闷头复习，一边又想知道外面发生了什么，但我没有时间每天刷 arXiv、翻 newsletter、看公众号，再自己去重和整理。最后要么彻底不看，要么掉进信息流看了很多热闹，还是说不清自己关心的细枝末节发生了什么。

公众号、小红书的推荐系统当然是很 nb 的东西，我也喜欢看。它们敏感、鲜活，虽然未必总是严谨。但为了严谨把每次更新都写成小论文，日报也会变得又臭又长。

我想要的更像小时候听小度播新闻。那是闹钟的一部分：先把我叫醒，接着播当天最热门的通用新闻。我不用想关键词，也不用打开十几个网页。后来它好像不怎么播了，但那种信息在固定时间主动来到面前的体验，我一直记得。

所以我有点武断地认为，这不只是我一个人的问题。时间之外，英语、订阅、机构权限、渠道和术语也在制造信息差。同一件新鲜事，有人第一时间看到，有人几个月后才从二手转述里知道。这个猜测还要留给真实使用验证，但我已经想到了一个比 AI assistant UI 更合适的形态：邮报。

“邮报”听起来有点复古，但人类很早就在定期整理信息。古罗马的 Acta Diurna 把公共事件写出来供人阅读；[^1] 中国的邸报、京报则长期传递诏令、奏章与朝廷动态。[^2] 社会越复杂，人就越需要一份整理过的信息。

到了现代报业，报纸开始进入大众日常。1896 年创刊的 Daily Mail 只卖半便士（约为当时多数伦敦日报价格的一半；按一般消费价格购买力粗略折算，相当于今天约 0.35 英镑，也就是人民币 3 元左右），内容也更短、更好读。[^3] 更多人买得起、读得完，信息才更容易成为日常。

内参走的是更窄、更密的一条路：为特定决策者整理会影响判断的信息。[^4] 今天的 newsletter 则让每个人订阅自己关心的领域，并与出版者保持直接关系。[^5] 可知道去哪里找、该订什么，本身又是一种信息差。

报纸为什么能赢，AI 报纸会是什么
======

传统报业进入大众生活，先靠技术把成本降下来。Library of Congress 的历史资料提到，蒸汽印刷催生了 penny press，让工人阶层也买得起报纸；后来的 linotype 又让排版和印刷更快，一期能装下更多新闻。[^10]

低价和产量只负责把报纸送到手里，编辑能力才决定人会不会留下。UNESCO 把编辑、事实核查和来源审视视为可靠信息的重要生产过程；Reuters Institute 的订阅研究也发现，每日使用是续订的重要预测因素，低价优惠能吸引第一次付费，真正留住人的仍是质量、易用性和习惯。[^11]

把这些放在一起，传统报业形成优势靠的是一整套组合：付得起的价格、广泛而稳定的分发、编辑筛选与核查、清楚的版面层级，以及日复一日积累的信任。

那么，AI 报纸会是什么？我的理解是，AI 在后台持续盯住远超个人能力的信源，完成去重、关联、翻译和初步解释；每一期再像编辑部一样做取舍，分出头版、简讯和留待细读的内容，标清原文、模型归纳与不确定性。它按固定节奏送到用户面前，也从阅读、跳过和收藏中慢慢学会 taste，同时保留一点意料之外的东西。用户最后收到一份能读完、能追溯、越来越懂自己的报纸。

FrontPost 是什么
======

FrontPost，前沿邮报，一份可以高度定制、持续追踪、每天或每周主动送达的 AI 邮报。

它像一套 AI 时代的小型报业系统。分析智能体和自动化任务在后台采集、筛选和解释信息，用户收到一份有重点、有来源、排版清楚的报纸。

它面向所有希望了解前沿信息的人。哪怕没有研究背景，也不熟悉复杂的检索和调研流程，照样可以打开就读。

这里的前沿包括论文、产品更新、开源项目、工程实践、行业变化、政策、市场和个人判断。信源可以来自 arXiv、Web of Science、用户已有权限的非 OA 数据库，也可以来自 RSS、newsletter、Substack、知乎、公众号和具体网站。[^6]

以我为例，我会同时关注具身智能、供应链、Agent、Text-to-SQL 和怎么开一家土耳其餐厅。每个人的组合与 taste 都不同，而且一开始未必说得清楚。FrontPost 会从阅读、跳过、收藏和追问中逐渐理解用户，也帮助用户发现真正关心的方向，在长期送报和反馈中慢慢同频。

它是一个 side project，也是我想认真试试的小产品。我会先把自己当作测试样例，再找几位可能有类似困扰的人一起使用。第一版先守住精准和轻量，其他判断交给真实反馈。

论文是重要的信源和证据，读者更常关心的则是“这件事意味着什么，和我有什么关系”。一些研究者、开发者和行业从业者的 Twitter、博客和 newsletter，公司技术博客、GitHub release notes、行业研报、标准、政策和市场数据，也会及时暴露新的变化。并非每个领域都有 arXiv 这样的开放生态，CS 确实是一个很有开源精神的领域。

说实话，我自认就是那种又菜又八卦的人：论文天天看真的会想吐，哪里有新东西又总想凑过去看一眼，更何况这还是一份早报。十篇论文、三十个创新点摆满整版，我大概根本不想点开。我想开开心心地看看今日份前沿头条，几分钟知道发生了什么，然后安心去做自己的事，也没有“未读 99+”的负债感。

轻量意味着合理分配注意力。有些内容值得二十秒，有些值得两分钟，强相关的新工作也可能值得十分钟。简报可以长如内参，也可以短如前线战报，篇幅取决于用户的时间、场景和 taste。最重要的内容放在头版头条，其他内容缩成简讯或留待展开。

FrontPost 会根据阅读、收藏、跳过和反馈调整推荐与排版，逐渐形成“这个人的邮报”，同时保留少量意料之外的新东西。

正如我在 [FrontPost 设计规范](https://design.frontpost.net/) 里写下的：**前沿知识，平等可达。** 我说的知识平权其实很简单：只要一个人想了解前沿，就应该能用付得起的价格、容易获取的方式，读到有来源、有解释的好内容。学生、开发者、行业从业者，或者只是对某件事好奇的人，都在里面。英语水平、所在地区、机构订阅、熟不熟悉某个圈子和工具，这些差别会造成真实的信息差，FrontPost 想尽量把它们抹平。这个目标现在还做不到，但它会一直决定 FrontPost 往哪里走。

定价也可以先按这个方向设计。基础订阅每月 2 美元，按一份便宜的数字报纸来定价；小学、中学和大学阶段的学生完成认证后半价，每月 1 美元；邀请码用户按 75 折，每月 1.5 美元。

地区优惠可以按世界银行每年更新的 Atlas method 人均 GNI 与收入分组判断（FY27 将低收入经济体划为 1,175 美元及以下，中低收入经济体划为 1,176–4,635 美元）[^9]：低收入和中低收入经济体半价，其中人均 GNI 低于 2,500 美元的经济体免费。半价可以先通过 IP 粗略判断地区，免费资格再结合 IP 与必要的身份认证，并尽量少收集和保存个人信息。这条 2,500 美元的免费线是 FrontPost 自己设定的公益方案，也希望让知识平权先在价格和获取门槛上真正发生。

![满头问号的啾啾](/images/2026/rest/confused.jpg)

*啾啾状态：认真想，从 0 开始，它应该变成什么样子？*

MVP
======

李自然公开的《AI 论文简报》方法论给了我不少启发：多信号筛选、解释内容意义、保留来源，同时公开自动化筛选可能遗漏小众工作的局限。[^7] FrontPost 会结合社区信号与个人阅读反馈，优先处理用户自己的细分关注。

这个目标很远，第一版还是先做好一件具体的事。现在已经有不少产品把不同环节做得很深。PaperDaily、论论全球、AMiner 和 Semantic Scholar 覆盖搜索、筛选、推荐与提醒；Consensus、Elicit、Perplexity 和玻尔把证据综合、深度调研与科研工作流串起来；Moonlight、ResearchRabbit、Feedly AI 和 Readwise Reader 则分别处理论文阅读、关系探索、持续追踪与信息汇聚。[^8]

我想参考它们解决问题的方式，FrontPost 的主形态仍然是一份持续送达的邮报。MVP 先聚焦用户关心领域的持续追踪，把新增变化筛选、编辑成一份轻量邮报并按时送达，不去沾染论文精读、知识归档、系统调研和综述等能力。

FrontPost 也希望建立以 AI-Native 高效协作为基础的组织方式，跟着真实反馈持续迭代。

最后
======

长期愿景可以放得远一些，眼下先让它在小范围里跑起来，再根据真实使用不断调整；如果进展顺利，我希望能在 2026 年做出一个全平台 MVP，跑通完整而可控的闭环，也借这个过程看清哪些判断成立、哪些还需要修正，慢慢学习如何把一个想法做成真正的产品。

![满血复活的啾啾](/images/2026/rest/recharged.jpg)

*啾啾状态：元气满满开干！*

[^1]: 关于 Acta Diurna，可参考 [Acta Diurna](https://en.wikipedia.org/wiki/Acta_Diurna) 和 [History of Information 的条目](https://www.historyofinformation.com/detail.php?entryid=1636)。不同资料对它的起始时间有不同说法，但通常都把它视为古罗马重要的早期公共信息发布形态。
[^2]: 关于中国邸报/京报，可参考 [EBSCO 对中国早期报纸的概述](https://www.ebsco.com/research-starters/history/first-newspapers-china) 和 [Dibao 词条](https://en.wikipedia.org/wiki/Dibao_%28ancient_Chinese_gazette%29)。邸报起源在学界有汉代说、唐代说等差异，这里只取“古代中国长期存在官方信息传递系统”这一点。
[^3]: Daily Mail 1896 年由 Alfred 和 Harold Harmsworth 创办，可参考 [DMGT history](https://www.dmgt.com/about-us/our-history/) 与 [Oxford Bodleian 的 Daily Mail Historical Archive 介绍](https://blogs.bodleian.ox.ac.uk/history/2018/02/16/new-daily-mail-historical-archive-1896-2004/)。旧制半便士等于 1/480 英镑；按 [英国央行通胀计算器](https://www.bankofengland.co.uk/monetary-policy/inflation/inflation-calculator) 使用的一般消费价格购买力口径及 [ONS 综合价格指数换算](https://www.officialdata.org/uk/inflation/1896?amount=1)，1896 年的 1 英镑约相当于 2025 年的 170 英镑，因此半便士约为今天的 0.35 英镑。再按 2026 年 7 月 [英镑兑人民币汇率](https://www.xe.com/en-us/currencyconverter/convert/?Amount=1&From=GBP&To=CNY) 粗算，约为人民币 3.2 元；换用工资、收入或财富口径会得到不同结果。
[^4]: 关于内参，可参考《人民周刊》文章 [《直达中南海的内参，怎样影响中国》](https://paper.people.com.cn/rmzk/html/2016-05/04/content_1676165.htm)；另见 Tang Yong, [For Whose Eyes Only?: China's Journalistic Internal Reference and Its Legal and Political Implications](https://scholarlypublishingcollective.org/psup/information-policy/article/doi/10.5325/jinfopoli.7.2017.0001/314465/For-Whose-Eyes-Only-China-s-Journalistic-Internal)。
[^5]: 关于 newsletter 在媒体中的重新兴起，可参考 Reuters Institute 的 [Editorial Email Newsletters: The Medium is Not the Only Message](https://reutersinstitute.politics.ox.ac.uk/our-research/editorial-email-newsletters-medium-not-only-message) 和 Digital News Report 2020 中的 [The Resurgence and Importance of Email Newsletters](https://reutersinstitute.politics.ox.ac.uk/digital-news-report/archive/survey/2020/the-resurgence-and-importance-of-email-newsletters/index.html)。
[^6]: 不同学术信源提供的能力并不一样。arXiv 是开放获取的预印本仓库；Web of Science Core Collection 则主要是覆盖期刊、图书和会议文献的引文数据库，很多内容的进一步访问取决于机构订阅。可参考 [arXiv 介绍](https://info.arxiv.org/about/index.html) 与 [Web of Science Core Collection 帮助文档](https://webofscience.help.clarivate.com/en-us/Content/wos-core-collection/wos-core-collection.htm)。
[^7]: 李自然，[《AI 论文简报：方法论》](https://ai-brief.liziran.com/zh/methodology)。该页面也公开说明了它目前基于标题和摘要解读、采用 T+3 发布，以及自动化筛选可能漏掉缺少社区信号的小众高价值工作等局限。
[^8]: 这里按各产品当前的官方介绍做了粗略归类：[PaperDaily / 知新](https://paperdaily.org/library?field_id=23)、[论论全球](https://www.lunlunapp.com/paperLib/)、[AMiner](https://www.aminer.cn/) 和 [Semantic Scholar](https://www.semanticscholar.org/product/) 覆盖论文搜索、解读、推荐或提醒；[Consensus](https://help.consensus.app/en/articles/9922673-how-consensus-works)、[Elicit](https://elicit.com/)、[Perplexity](https://www.perplexity.ai/help-center/en/articles/10352895-how-does-perplexity-work) 与 [玻尔](https://www.bohrium.com/intro) 提供带来源的问答、证据综合、深度调研或更完整的科研任务；[Moonlight](https://www.themoonlight.io/zh)、[ResearchRabbit](https://www.researchrabbit.ai/)、[Feedly AI](https://feedly.com/ai) 和 [Readwise Reader](https://readwise.io/read) 分别侧重论文阅读、文献关系探索、主题追踪和多来源阅读汇聚。产品会继续变化，这里只记录 2026 年 7 月查到的公开能力。
[^9]: [世界银行 FY27 收入分组](https://datahelpdesk.worldbank.org/knowledgebase/articles/906519-world-bank-country-and-lending-groups)依据 Atlas method 人均 GNI 划分经济体，并在每年 7 月更新。这里的 2,500 美元是 FrontPost 暂定的公益免费线，不是联合国“最不发达国家”的官方标准；[联合国相关标准](https://www.un.org/development/desa/dpad/least-developed-country-category/ldc-criteria.html)还会综合人均 GNI、人力资产与经济环境脆弱性。
[^10]: 美国国会图书馆在 [A History of Obituaries in American Newspapers](https://blogs.loc.gov/headlinesandheroes/2026/05/mourn-not-a-history-of-obituaries-in-american-newspapers/) 中提到，蒸汽印刷降低了报纸价格，推动 penny press 进入工人阶层；linotype 又提高了排版与印刷效率，使一期报纸可以容纳更多新闻、广告和普通人的故事。这里引用的是它对报业生产与受众扩张的历史梳理。
[^11]: UNESCO 的 [Balancing Act](https://unesdoc.unesco.org/ark:/48223/pf0000377231) 把编辑、事实核查、来源审视与专业策展视为可靠新闻的重要生产能力。Reuters Institute 的 [Paying for news](https://reutersinstitute.politics.ox.ac.uk/paying-news-price-conscious-consumers-look-value-amid-cost-living-crisis) 研究则指出，规律的每日使用是持续订阅的重要预测因素；低价优惠有助于首次转化，内容质量、易用性和有组织的阅读体验更关系到长期留存。
