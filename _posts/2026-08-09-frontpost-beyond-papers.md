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

上一篇写 [FrontPost 的方法论](/posts/frontpost-methodology) 时，我真正想说的不是“好期刊一定不好”或者“论文没有用”。期刊名次、引用数、机构声誉、社交热度和 GitHub star 都有一点信息量，但只能作为有上限的辅助信号：它们可以帮我们决定先看哪里，不能替我们判断一项研究是否重要、严谨、可复现，或者是否真的带来了新东西。

所以我后来又想了一步：**研究也不等于论文。** 论文是一种重要的交流形式，但研究还会发生在代码、数据、实验日志、博客、论坛、比赛和社区协作里。学术界还有不少由评价体系催生出来的二级问题：怎样包装更像创新，榜单高零点几能不能再发一篇，什么故事更讨审稿人喜欢。这些问题确实影响发表，却不一定帮助人把事情做成。

真正做事的人往往更关心：它到底有没有用，原来的方法哪里不行，换个环境还能不能工作，我能不能接着做下去。

## 研究发生在很多地方

Keller Jordan 解释自己没有为 Muon 写正式 arXiv 论文时，大意是：一篇优化器论文能否做出许多漂亮结果，与这个优化器是否真的有效，并没有必然关系；他更信任 speedrun。[^1]

我不把这句话当真理，只把它看作一个实干者的观点。Muon 最初通过公开讨论、博客、代码和训练竞速传播，社区随后不断复现、改进和质疑。modded-nanogpt 的成绩表连着代码、日志和贡献者：谁改了优化器，谁优化了注意力，效果如何，都可以继续检查。[^2]

llama.cpp 也是类似的例子。它先是一个 GitHub 项目，却很快让本地运行大模型从少数人的演示变成真实的工程生态。OpenFold 由社区把蛋白质结构预测能力做成可训练、可审计的开放基础设施。LAION 的开放数据支持了许多多模态项目，也在外部审查发现严重问题后撤下、清理和重新发布。[^3]

这些案例并不说明博客或 GitHub 天生比论文可靠。它们只是提醒我，有价值的工作不必先等某个入口批准才存在；而一旦公开，别人就有机会继续验证、改进，甚至证明它不对。

我更愿意把社区看成一个公共实验场，而不是新的权威。它最有用的地方，是让任何人都有机会参与检查。

## 我想把 FrontPost 做成什么样

传统报业判断一件事是否值得上版面，会看新鲜、影响、相关和反常，而不只看发布者的头衔。[^4] 前沿采编也应该如此。

名校、榜单和热度可以让一篇 SOTA 论文值得先打开，但打开之后仍然要看：它到底比已有工作多了什么，指标稳不稳，成本和限制在哪里。相反，一个没有论文的仓库，如果提供了方法、代码、日志和限制，也可能更接近真实进展。

所以我不想把 FrontPost 做成一台论文摘要机。我更想先试一条简单的采编链路：

1. **先从一句人话开始。** 用户只需要说“我在做端侧多模态模型，想关注推理优化和真实设备测试”。FrontPost 再把它展开成主题、人物、项目、社区、排除项和时间范围。用户不必一开始就知道这个领域所有该关注的人。

2. **别只盯着论文。** 论文、官方文档、博客、代码仓库、issue、release、比赛、论坛和 newsletter 都可以成为线索。系统要慢慢记住：谁最早提出过一个想法，谁真的复现过，谁经常提出有用的反例。同时留一点位置给没那么熟的来源，不要让推荐系统越用越窄。

3. **先弄清楚它到底说了什么。** 研究 Agent 把内容拆成一条条可以检查的陈述，分清原文事实、作者观点、工具自述和模型推断，再去找代码、数据、实验条件和独立复现。期刊名、机构、引用数、star 和社交热度可以帮忙找线索，但不能替证据说话。

4. **让另一个 Agent 专门挑刺。** 它不只问“厉不厉害”，还要追问：相比已有工作多了什么，基准公平吗，成本藏在哪里，结论能外推吗，有没有不太好看的结果。最好再去找反例、版本变化和互相矛盾的来源，免得被夸夸其谈的 SOTA 牵着走。

5. **最后才是写成一份读得下去的东西。** 不只告诉你发生了什么，还要说清楚它为什么和你有关，证据有多强，哪里仍不确定，接下来可以读什么、试什么。重要变化放前面，暂时只值得扫一眼的就写短一点，原始链接始终留着。

我不觉得这需要一个无所不能的 Agent。让几个小角色各自做擅长的事，反而更容易看清哪里出了问题：有人找线索，有人整理证据，有人挑刺，最后由人决定要不要相信、要不要继续追。

我最近也在看 AI4Science Agent、PaperQA2 和 ARIS 这类工作流。它们让我想到，FrontPost 以后也许不只是每天生成一份摘要，而是让每个关注方向慢慢长成一条线索：有新来源就补进来，有反例就记下来，人睡觉时继续跑一轮，早上交给人一小段能接着往下走的进展。这个想法还很早，也不代表 FrontPost 已经是什么 AI4Science 系统；先借一点方法，具体怎么做再慢慢试。[^5][^6][^7]

我还在想，什么才算 AI-native。不是给旧报纸接一个大模型，然后每天自动生成一版“AI 早报”。那只是把编辑部的活儿搬到后台，读者还是被动收一堆没人真正想看的东西。

旧报纸的基本单位是“一期”，FrontPost 也许不该从一期开始。它更像是一个人正在关心的问题，或者一条还没有结束的研究线索。今天看到一个项目，明天补进一篇反例，过几天发现它和另一个领域有关系，用户还可以继续追问、收藏、跳过，甚至直接拿去做自己的实验。信息不再是发完就结束的文章，而是可以继续生长的东西。

这样一来，我们真正要解决的也许不是“怎样让人每天看更多信息”，而是“怎样把分散的知识、人的注意力和机器的算力，尽量变成一次有用的理解或行动”。算力不能只用来把每篇摘要写得更漂亮。可以先用便宜的模型做大范围发现、去重和初筛，再把昂贵的模型留给高相关、高不确定的候选；能缓存的证据就不重复读，能并行的检索就一起做，值得继续追的线索才进入更深的 Agent 循环。最后看的指标也不该只是用了多少 token，而是这些算力有没有让人少走一步弯路，或者真的开始做一件事。

## 接下来还可以怎么做

如果继续做下去，我最想留下的不是一个神秘总分，而是一张会慢慢变好的信源图谱和主张记录。

当用户收藏、跳过、纠正，或者真的拿某条信息去做事时，FrontPost 才有机会慢慢学会：哪些社区更早出现有效信号，哪些作者真的会复现，哪些来源总在重复公关话术。同一个主张后来有了新证据，也应该回到原来的记录里更新，而不是再生成一篇互不相干的摘要。

我也希望它保留一点“意外发现”的能力。精准不等于只给人看已经同意的东西。每一期可以留一点位置给邻近领域的怪想法、没赢得榜单却改变了问题定义的工作，还有一些少数人认真做着的尝试。好的信息不只是帮人省时间，有时也会让人突然想做点什么。

一个刚入门的人，没必要先花几年混圈子，才有资格知道真正的前沿在哪里。传统社交媒体要先训练推荐系统；我更希望 FrontPost 从一句话开始，慢慢跟着人的反馈变得懂一点。

说到底，我不想继续转发空洞的“重大突破”、名次崇拜和术语门槛。我想找的是准确、有趣、有新意，而且让人愿意继续动手的信息。不是替谁找一个新的权威，只是别让真正有价值的工作总是来得太晚。

[^1]: Keller Jordan 的说明由 Varun Neal 在 [Muon in Modded NanoGPT](https://varunneal.github.io/essays/muon) 中引用。这里按其含义转述，不把个人观点当作普遍结论。
[^2]: Keller Jordan，[Muon: An optimizer for hidden layers in neural networks](https://kellerjordan.github.io/posts/muon/)；实现和后续资料见 [KellerJordan/Muon](https://github.com/KellerJordan/Muon)，公开训练竞速见 [KellerJordan/modded-nanogpt](https://github.com/KellerJordan/modded-nanogpt)。
[^3]: 相关项目见 [ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp)、[OpenFold](https://openfold.io/)、LAION 的 [DataComp 发布说明](https://laion.ai/blog/datacomp/) 与 [Re-LAION-5B 清理说明](https://laion.ai/blog/relaion-5b/)。开放项目同样会犯错，这里关注的是公开复现和纠错机制。
[^4]: Tony Harcup 与 Deirdre O’Neill 对新闻价值的研究列出了 surprise、magnitude、relevance 等常见因素，见 [What is News? News Values Revisited (Again)](https://www.tandfonline.com/doi/abs/10.1080/1461670X.2016.1150193)。
[^5]: Google Research，[Accelerating scientific breakthroughs with an AI co-scientist](https://research.google/blog/accelerating-scientific-breakthroughs-with-an-ai-co-scientist/)；Skarlinski 等，[Language agents achieve superhuman synthesis of scientific knowledge](https://arxiv.org/abs/2409.13740)；ARIS 项目仓库 [Auto-claude-code-research-in-sleep](https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep) 及其早期技术报告 [ARIS: Autonomous Research via Adversarial Multi-Agent Collaboration](https://arxiv.org/abs/2605.03042)。前两者有公开论文或评测，ARIS 的具体工作流和能力边界仍应按开源项目与报告理解，不代表 FrontPost 已具备同类能力。
[^6]: Google Research，[Towards a science of scaling agent systems: When and why agent systems work](https://research.google/blog/towards-a-science-of-scaling-agent-systems-when-and-why-agent-systems-work/)。该研究报告多 Agent 在可并行任务上收益明显，但在顺序任务上可能退化。
[^7]: Buchmann 等，[Evaluating and Guarding Citation Faithfulness in Agentic Scientific Synthesis](https://arxiv.org/abs/2607.20527)。论文报告同一批输出在不同验证器下测得的不受支持引用比例约为 3%–18%，说明验证器本身也需要校准。
