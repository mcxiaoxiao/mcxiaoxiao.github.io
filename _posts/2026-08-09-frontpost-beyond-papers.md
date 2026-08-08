---
title: '我们需要怎样的前沿信息'
date: 2026-08-09
permalink: /posts/frontpost-beyond-papers
tags:
  - FrontPost
  - AI
  - 产品
  - 随想
---

上一篇写 [FrontPost 的方法论](/posts/frontpost-methodology) 时，我提到：好期刊不等于好研究。这里再补一句，**研究也不等于论文。**

论文是一种重要的交流形式，但研究还会发生在代码、数据、实验日志、博客、论坛、比赛和社区协作里。很多学术问题则是评价体系制造出来的伪命题：怎样包装更像创新，榜单高零点几能不能再发一篇，什么故事更讨审稿人喜欢。这些问题影响发表，却未必帮助人把事情做成。

实干者更关心的是：它到底有没有用，原来的方法哪里不行，换个环境还能不能工作，我能不能接着做下去。

## 研究发生在很多地方

Keller Jordan 解释自己没有为 Muon 写正式 arXiv 论文时，大意是：一篇优化器论文能否做出许多漂亮结果，与这个优化器是否真的有效，并没有必然关系；他更信任 speedrun。[^1]

我不把这句话当真理，只把它看作一个实干者的观点。Muon 最初通过公开讨论、博客、代码和训练竞速传播，社区随后不断复现、改进和质疑。modded-nanogpt 的成绩表连着代码、日志和贡献者：谁改了优化器，谁优化了注意力，效果如何，都可以继续检查。[^2]

llama.cpp 也是类似的例子。它先是一个 GitHub 项目，却很快让本地运行大模型从少数人的演示变成真实的工程生态。OpenFold 由社区把蛋白质结构预测能力做成可训练、可审计的开放基础设施。LAION 的开放数据支持了许多多模态项目，也在外部审查发现严重问题后撤下、清理和重新发布。[^3]

这些案例并不说明博客或 GitHub 天生比论文可靠。它们说明的是：有价值的工作不必等待某个入口批准才存在，开放也意味着任何人都可以继续验证和纠错。

社区不是新的权威。社区的价值，是让权威无法垄断检查。

## FrontPost 应该怎样找信息

传统报业判断一件事是否值得上版面，会看新鲜、影响、相关和反常，而不只看发布者的头衔。[^4] 前沿采编也应该如此。

一篇名校团队的 SOTA 论文，如果只是换了包装、堆了算力、提高一个脆弱指标，未必比开发者公开的反例更值得占据头版。一个没有论文的仓库，如果提供了方法、代码、日志和限制，也可能更接近真实进展。

FrontPost 因此不能只做一台论文摘要机。它需要一套更完整的 AI 采编流程。

1. **把一句话变成关注范围。** 用户只需要说“我在做端侧多模态模型，想关注推理优化和真实设备测试”。系统把它展开成主题、人物、项目、社区、排除项、时间范围和期望深度，不要求用户先知道所有专业信源。

2. **同时寻找正式与非正式来源。** 来源雷达持续发现论文、官方文档、博客、代码仓库、issue、release、比赛、论坛和 newsletter，并记录“谁最早提出、谁复现过、谁经常提出有效反例”。大部分注意力给高相关来源，也保留一小部分探索额度，避免推荐系统越用越窄。

3. **按主张核验，而不是按载体站队。** 研究 Agent 把内容拆成可检查的陈述，区分原文事实、作者观点、工具自述和模型推断，再寻找代码、数据、实验条件和独立复现。期刊名、机构、引用数、star 和社交热度都可以提供线索，但不能代替证据。

4. **让编辑 Agent 对抗炒作。** 编辑不只问“厉不厉害”，还要问：相比已有工作到底多了什么，基准是否公平，成本有没有被隐藏，结论能否外推，有没有负面结果。另一个审校 Agent 专门寻找反例、版本变化和互相矛盾的来源，避免被夸夸其谈的 SOTA 牵着走。

5. **把信息编辑成可以行动的内容。** 输出不应是一堆摘要，而应说明：发生了什么，为什么和你有关，证据有多强，哪里仍不确定，接下来可以读什么、试什么。头条负责重要变化，简讯负责快速扫读，原始链接始终保留。

这套流程不需要一个无所不能的 Agent。更实际的做法是让信源发现、证据整理、编辑判断和反向审校彼此分工，保存每一步的来源和理由。AI 负责扩大视野、压缩重复劳动，人保留最终的价值判断。

## 从 AI4Science Agent 借一点思路

这些想法和近年的 AI4Science Agent 有些相邻。Google 的 AI co-scientist 让生成、反思、排序和 meta-review 等角色共同改进候选假设；PaperQA2 则把检索、带引用的综合和文献矛盾发现连成流程。[^5] 它们面向科学假设和知识综合，FrontPost 目前做的仍是更靠前、也更朴素的信息发现与采编，不能直接画等号。

其中的循环值得借鉴：先建立候选池，让不同角色寻找证据、反例和遗漏，再由编辑整合。一次运行不只留下成稿，也留下“问题—来源—主张—争议—下一步”的路径，让用户知道内容怎样得来、还缺什么。

但多 Agent 不是越多越好。对 180 种配置的控制实验显示，协作适合可并行任务，在连续任务上反而可能降低表现。[^6] 另一项研究则发现，同一批科学综合结果，仅因引用验证器不同，测得的不受支持引用比例就可能从约 3% 变到 18%。[^7] 因此，信源发现可以并行，证据合并与编辑决策应保持清楚的顺序；自动核验要记录规则和版本，重要判断仍然交给人。

所以我暂时不会把 FrontPost 称为 AI4Science。我们只是遇到了一部分相似问题，也许能借一点方法。先把基本工作做好，再看它会把我们带到哪里。

## 接下来还可以怎么做

FrontPost 后续最值得积累的不是一个神秘总分，而是一张不断更新的信源图谱和主张记录。

当用户收藏、跳过、纠正或真的采用某条信息时，系统不仅调整关键词，还要学习哪些社区更早出现有效信号、哪些作者善于做可靠复现、哪些来源总在重复公关话术。遇到同一主张的新证据时，系统应当更新它的时间线，而不是再生成一篇孤立摘要。

我也希望 FrontPost 保留“意外发现”的能力。精准不是只给人看已经同意的东西。每一期都可以留一点位置给邻近领域的怪想法、没有赢得榜单却改变问题定义的工作，以及少数人的认真尝试。好的信息不只提高效率，也会激发创造力。

一个刚入门的人，不该先花几年混圈子，才有资格知道真正的前沿在哪里。传统社交媒体需要用户慢慢训练推荐系统；FrontPost 希望把起点缩短成一句话，再用持续反馈变得更懂用户。

我们想减少的是空洞的“重大突破”、名次崇拜和术语门槛，增加的是准确、有趣、有新意、可以继续动手的信息。不是替用户找到新的权威，而是让真正有价值的工作更早被看见。

[^1]: Keller Jordan 的说明由 Varun Neal 在 [Muon in Modded NanoGPT](https://varunneal.github.io/essays/muon) 中引用。这里按其含义转述，不把个人观点当作普遍结论。
[^2]: Keller Jordan，[Muon: An optimizer for hidden layers in neural networks](https://kellerjordan.github.io/posts/muon/)；实现和后续资料见 [KellerJordan/Muon](https://github.com/KellerJordan/Muon)，公开训练竞速见 [KellerJordan/modded-nanogpt](https://github.com/KellerJordan/modded-nanogpt)。
[^3]: 相关项目见 [ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp)、[OpenFold](https://openfold.io/)、LAION 的 [DataComp 发布说明](https://laion.ai/blog/datacomp/) 与 [Re-LAION-5B 清理说明](https://laion.ai/blog/relaion-5b/)。开放项目同样会犯错，这里关注的是公开复现和纠错机制。
[^4]: Tony Harcup 与 Deirdre O’Neill 对新闻价值的研究列出了 surprise、magnitude、relevance 等常见因素，见 [What is News? News Values Revisited (Again)](https://www.tandfonline.com/doi/abs/10.1080/1461670X.2016.1150193)。
[^5]: Google Research，[Accelerating scientific breakthroughs with an AI co-scientist](https://research.google/blog/accelerating-scientific-breakthroughs-with-an-ai-co-scientist/)；Skarlinski 等，[Language agents achieve superhuman synthesis of scientific knowledge](https://arxiv.org/abs/2409.13740)。这里引用的是系统公开的方法和评测，不代表 FrontPost 已具备同类能力。
[^6]: Google Research，[Towards a science of scaling agent systems: When and why agent systems work](https://research.google/blog/towards-a-science-of-scaling-agent-systems-when-and-why-agent-systems-work/)。该研究报告多 Agent 在可并行任务上收益明显，但在顺序任务上可能退化。
[^7]: Buchmann 等，[Evaluating and Guarding Citation Faithfulness in Agentic Scientific Synthesis](https://arxiv.org/abs/2607.20527)。论文报告同一批输出在不同验证器下测得的不受支持引用比例约为 3%–18%，说明验证器本身也需要校准。
