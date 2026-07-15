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

[上上篇](/posts/llmapi-gateway)还在想 LLM API Gateway，甚至很认真地研究了多模型接入、任务状态、成本、合规和各种网关方案。但越往下想，越觉得它对现在的我来说可能有点太重了。它当然是个真实问题，只是要把它做好，需要碰的基础设施、企业需求和治理边界都太多，不太像一个适合我在读研期间轻装试水的项目。

这次的想法叫 FrontPost。它离我的日常更近，也让我产生了一种“这个我真的很想用，所以很想尽快做出来”的冲动。

为什么我会想要这个东西
======

因为这基本就是我自己的痛点。

我并不喜欢又臭又长的 paper。至少在确认一篇东西真的值得读之前，我不想先花半天把它从头啃到尾。我可能就是那种专注度不太够、又什么都想知道一点的人。说自己是 AI researcher 有点夸张，毕竟我现在只是个普通计算机学生，但即便如此，我也能感觉到技术进展快得有点吓人。

这种害怕其实很朴素：几个月没看，怎么感觉整个世界又换了一套说法？

考研这一年尤其明显。一边要闷头复习，一边又想知道外面发生了什么，但我根本没有时间每天刷 arXiv、翻 newsletter、看一堆公众号，再自己去重、判断和整理。最后通常只有两种状态：要么彻底不看，心里总觉得自己正在错过时代；要么在休息时掉进信息流，知道了很多热闹，但还是说不清自己真正关注的那些细枝末节最近发生了什么。

公众号、小红书之类的推荐系统当然是很 nb 的东西，我也很喜欢看。它们敏感、鲜活、知道什么东西会让人点进去，虽然未必总是严谨。但另一个极端我也不喜欢：为了显得严谨，把每一次更新都写成一篇小论文，最后日报也变得又臭又长。

我想要的其实更像小时候每天听小度给我播新闻。那是它闹钟功能的一部分：每天先把我叫醒，接着就开始播当天的新闻。我不用先想好关键词，也不用打开十几个网页。后来它好像不怎么播了，我也不知道是不是百度放弃了这个服务。它播的是最热门的通用新闻，而那种信息在固定时间主动来到面前的体验，我一直记得。

所以我有点武断地认为，这可能不只是我一个人的问题。也许还有一些人同样想跟上自己关心的前沿，却没有时间和注意力每天整理所有信源。当然，这现在只是一个从自己出发的猜测，是否真的如此、产品形态是否贴合需求，还要等做出来，再在真实使用里慢慢验证和对齐。

如果要给这种需求找一个合适的形态，我最先想到的是邮报，一种区别于 AI assistant UI 的产品形态。

更准确地说，我想做一个 AI 邮报。

“邮报”这个词听起来有点复古，但我越想越觉得它很适合。人类处理信息过载这件事，其实从来不是今天才开始的。古罗马有 Acta Diurna，把元老院、法庭、公共事件之类的信息写出来，放在公共场所给人看；[^1] 中国很早就有邸报、京报这一类官方信息传递系统，把诏令、奏章、官员任免、朝廷动态从中央传到地方。[^2] 它们当然不是现代意义上的新闻自由，也不是今天的媒体产品，但它们说明了一件事：只要社会变复杂，人就会需要某种“定期整理后的公共信息”。

后来到了现代报业，报纸又从官方公告变成了大众日常。比如 1896 年创刊的 Daily Mail，它的重要之处在于把报纸做得更便宜、更短、更适合普通人的日常节奏。[^3] 半便士（约为当时多数伦敦日报价格的一半；按一般消费价格购买力粗略折算，约相当于今天的 0.35 英镑，也就是人民币 3 元左右）、简洁、面向更广泛的人群，这背后其实是一个产品判断：信息要以一种可持续消费的方式抵达人。

还有一个更小众但很重要的形态叫“内参”。它面向特定决策者，范围更窄，密度更高，重点在于整理公开报道里不一定能充分展开、却会影响判断的信息。中国新闻传播体制里的内参就是很典型的例子，人民日报旗下文章把它称为一种具有参考性质的内部信息传播载体和工作机制，用来帮助决策者了解民意和实际。[^4] 这也说明，信息产品既可以告诉所有人发生了什么，也可以给特定人群提供更高信噪比的判断材料。

再往后，互联网把信息生产和分发成本打到接近于零，问题反而从“缺信息”变成“信息太多”。传统报业和新媒体这些年重新重视 newsletter，也不是偶然。Reuters Institute 很早就提到，邮件 newsletter 可以让新闻机构和读者保持直接关系，有更强的定向能力，也更适合展示已有内容、原创分析和评论。[^5] 这也是为什么很多人会订自己关注领域的 newsletter：AI 的、产品的、投资的、科研的、政策的、行业的。它们在信息爆炸里帮人保留了几条可信、稳定、和自己相关的入口。

从罗马广场上的公示板，到中国官僚系统里的邸报和内参，再到工业时代的大众日报，再到今天的网页、RSS、Newsletter、推送和信息流，技术形态一直在变，但底层需求没怎么变：

  - 世界太大，单个人不可能自己盯住所有变化。
  - 信息太散，需要有人或系统做筛选、排序和解释。
  - 真正有价值的内容，应该按稳定节奏送到人面前。
  - 重要结论不能只靠“看起来很像真的”，它要能回到来源。

FrontPost 是什么
======

FrontPost，前沿邮报，一份可以高度定制、持续追踪、每天或每周主动送达的 AI 邮报。

它像一套 AI 时代的小型报业系统。分析智能体和自动化任务在后台采集、筛选和解释信息，用户收到一份有重点、有来源、排版清楚的报纸。

这里的前沿包括论文、产品更新、开源项目、工程实践、行业变化、政策、市场和个人判断。信源可以来自 arXiv、Web of Science、用户已有权限的非 OA 数据库，也可以来自 RSS、newsletter、Substack、知乎、公众号和具体网站。[^6]

以我为例，我会同时关注具身智能、供应链、Agent、Text-to-SQL 和怎么开一家土耳其餐厅。每个人的组合与 taste 都不同，而且一开始未必说得清楚。FrontPost 会从阅读、跳过、收藏和追问中逐渐理解用户，也帮助用户发现真正关心的方向，在长期送报和反馈中慢慢同频。

它是一个 side project，也是我想认真试试的小产品。我会先把自己当作测试样例，再找几位可能有类似困扰的人一起使用。第一版先守住精准和轻量，其他判断交给真实反馈。

论文是重要的信源和证据，读者更常关心的则是“这件事意味着什么，和我有什么关系”。dalao 的 Twitter、博客和 newsletter，公司技术博客、GitHub release notes、行业研报、标准、政策和市场数据，也会及时暴露新的变化。并非每个领域都有 arXiv 这样的开放生态，CS 确实是一个很有开源精神的领域。

说实话，天天看论文我可能会想吐，更何况这是早报。十篇论文、三十个创新点摆满整版，我大概根本不想点开。我想开开心心地看看今日份前沿头条，几分钟知道发生了什么，然后安心去做自己的事，也没有“未读 99+”的负债感。

轻量意味着合理分配注意力。有些内容值得二十秒，有些值得两分钟，强相关的新工作也可能值得十分钟。简报可以长如内参，也可以短如前线战报，篇幅取决于用户的时间、场景和 taste。最重要的内容放在头版头条，其他内容缩成简讯或留待展开。

FrontPost 会根据阅读、收藏、跳过和反馈调整推荐与排版，逐渐形成“这个人的邮报”，同时保留少量意料之外的新东西。

![满头问号的啾啾](/images/2026/rest/confused.jpg)

*啾啾状态：认真想，从 0 开始，它应该变成什么样子？*

MVP
======

李自然公开的《AI 论文简报》方法论给了我不少启发：多信号筛选、解释内容意义、保留来源，同时公开自动化筛选可能遗漏小众工作的局限。[^7] FrontPost 会结合社区信号与个人阅读反馈，优先处理用户自己的细分关注。

玻尔已经覆盖论文追踪、每日推送、精读、调研和综述；[^8] Perplexity 也有完整的 deep research。FrontPost 的 MVP 要聚焦信息的发现、筛选、泛读和送达，不去沾染论文精读、知识归档、系统调研和综述等能力。

FrontPost 希望建立以 AI-Native 高效协作为基础的组织形式，持续高效迭代这个 AI-Native 产品。

最后
======

或许更重要的是先让它在小范围里跑起来，再根据真实使用不断调整；如果进展顺利，我希望能在 2026 年做出一个全平台 MVP，跑通完整而可控的闭环，也借这个过程看清哪些判断成立、哪些还需要修正，慢慢学习如何把一个想法做成真正的产品。

![满血复活的啾啾](/images/2026/rest/recharged.jpg)

*啾啾状态：元气满满开干！*

[^1]: 关于 Acta Diurna，可参考 [Acta Diurna](https://en.wikipedia.org/wiki/Acta_Diurna) 和 [History of Information 的条目](https://www.historyofinformation.com/detail.php?entryid=1636)。不同资料对它的起始时间有不同说法，但通常都把它视为古罗马重要的早期公共信息发布形态。
[^2]: 关于中国邸报/京报，可参考 [EBSCO 对中国早期报纸的概述](https://www.ebsco.com/research-starters/history/first-newspapers-china) 和 [Dibao 词条](https://en.wikipedia.org/wiki/Dibao_%28ancient_Chinese_gazette%29)。邸报起源在学界有汉代说、唐代说等差异，这里只取“古代中国长期存在官方信息传递系统”这一点。
[^3]: Daily Mail 1896 年由 Alfred 和 Harold Harmsworth 创办，可参考 [DMGT history](https://www.dmgt.com/about-us/our-history/) 与 [Oxford Bodleian 的 Daily Mail Historical Archive 介绍](https://blogs.bodleian.ox.ac.uk/history/2018/02/16/new-daily-mail-historical-archive-1896-2004/)。旧制半便士等于 1/480 英镑；按 [英国央行通胀计算器](https://www.bankofengland.co.uk/monetary-policy/inflation/inflation-calculator) 使用的一般消费价格购买力口径及 [ONS 综合价格指数换算](https://www.officialdata.org/uk/inflation/1896?amount=1)，1896 年的 1 英镑约相当于 2025 年的 170 英镑，因此半便士约为今天的 0.35 英镑。再按 2026 年 7 月 [英镑兑人民币汇率](https://www.xe.com/en-us/currencyconverter/convert/?Amount=1&From=GBP&To=CNY) 粗算，约为人民币 3.2 元；换用工资、收入或财富口径会得到不同结果。
[^4]: 关于内参，可参考《人民周刊》文章 [《直达中南海的内参，怎样影响中国》](https://paper.people.com.cn/rmzk/html/2016-05/04/content_1676165.htm)；另见 Tang Yong, [For Whose Eyes Only?: China's Journalistic Internal Reference and Its Legal and Political Implications](https://scholarlypublishingcollective.org/psup/information-policy/article/doi/10.5325/jinfopoli.7.2017.0001/314465/For-Whose-Eyes-Only-China-s-Journalistic-Internal)。
[^5]: 关于 newsletter 在媒体中的重新兴起，可参考 Reuters Institute 的 [Editorial Email Newsletters: The Medium is Not the Only Message](https://reutersinstitute.politics.ox.ac.uk/our-research/editorial-email-newsletters-medium-not-only-message) 和 Digital News Report 2020 中的 [The Resurgence and Importance of Email Newsletters](https://reutersinstitute.politics.ox.ac.uk/digital-news-report/archive/survey/2020/the-resurgence-and-importance-of-email-newsletters/index.html)。
[^6]: 不同学术信源提供的能力并不一样。arXiv 是开放获取的预印本仓库；Web of Science Core Collection 则主要是覆盖期刊、图书和会议文献的引文数据库，很多内容的进一步访问取决于机构订阅。可参考 [arXiv 介绍](https://info.arxiv.org/about/index.html) 与 [Web of Science Core Collection 帮助文档](https://webofscience.help.clarivate.com/en-us/Content/wos-core-collection/wos-core-collection.htm)。
[^7]: 李自然，[《AI 论文简报：方法论》](https://ai-brief.liziran.com/zh/methodology)。该页面也公开说明了它目前基于标题和摘要解读、采用 T+3 发布，以及自动化筛选可能漏掉缺少社区信号的小众高价值工作等局限。
[^8]: 玻尔目前的产品介绍里已经包含关键词、期刊或学者订阅、每日推送与 AI 速读，并可以继续进入精读、调研和综述工作流。见 [玻尔产品介绍](https://www.bohrium.com/intro)。
