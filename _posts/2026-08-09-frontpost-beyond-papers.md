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

上一篇写 [FrontPost 的方法论](/posts/frontpost-methodology) 时，我想讨论的是如何看待期刊名次、引用数、机构声誉、社交热度和 GitHub star。这些指标都有一点信息量，但作用有限：它们可以帮助我们决定先看哪里，无法单独说明一项研究是否重要、严谨、可复现，或者是否真的带来了新东西。

所以我后来又想了一步：**研究也不等于论文。** 论文是一种重要的交流形式，但研究还会发生在代码、数据、实验日志、博客、论坛、比赛和社区协作里。学术界还有不少由评价体系催生出来的二级问题：怎样包装更像创新，榜单高零点几能不能再发一篇，什么故事更讨审稿人喜欢。这些问题确实影响发表，却不一定帮助人把事情做成。

真正做事的人往往更关心：它到底有没有用，原来的方法哪里不行，换个环境还能不能工作，我能不能接着做下去。

## 研究发生在很多地方

Keller Jordan 解释自己没有为 Muon 写正式 arXiv 论文时，大意是：一篇优化器论文能否做出许多漂亮结果，与这个优化器是否真的有效，并没有必然关系；他更信任 speedrun。[^1]

我不把这句话当真理，只把它看作一个实干者的观点。Muon 最初通过公开讨论、博客、代码和训练竞速传播，社区随后不断复现、改进和质疑。modded-nanogpt 的成绩表连着代码、日志和贡献者：谁改了优化器，谁优化了注意力，效果如何，都可以继续检查。[^2]

llama.cpp 也是类似的例子。它先是一个 GitHub 项目，却很快让本地运行大模型从少数人的演示变成真实的工程生态。OpenFold 由社区把蛋白质结构预测能力做成可训练、可审计的开放基础设施。LAION 的开放数据支持了许多多模态项目，也在外部审查发现严重问题后撤下、清理和重新发布。[^3]

这些案例不说明博客或 GitHub 比论文可靠，只说明有价值的工作不必先经过某个入口批准。公开之后，别人可以验证、改进，也可以证明它不对。

## 我想把 FrontPost 做成什么样

传统报业判断一件事是否值得上版面，会看新鲜、影响、相关和反常，而不只看发布者的头衔。[^4] 前沿采编也应该如此。

名校、榜单和热度可以让一篇 SOTA 论文值得先打开，但打开之后仍然要看：它到底比已有工作多了什么，指标稳不稳，成本和限制在哪里。相反，一个没有论文的仓库，如果提供了方法、代码、日志和限制，也可能更接近真实进展。

所以我不想把 FrontPost 做成论文摘要机。我想先试一条简单的采编链路：

1. **先从一句人话开始。** 用户说“我在做端侧多模态模型，想关注推理优化和真实设备测试”，FrontPost 再展开主题、人物、项目、社区、排除项和时间范围。

2. **别只盯着论文。** 论文、官方文档、博客、代码仓库、issue、release、比赛、论坛和 newsletter 都可以是线索。系统记录来源、复现、反例和版本变化，也避免推荐范围越用越窄。

3. **先弄清楚它到底说了什么。** 研究 Agent 把内容拆成可检查的陈述，区分原文事实、作者观点、工具自述和模型推断，再查代码、数据、实验条件和独立复现。期刊名、机构、引用数、star 和社交热度只能帮助找线索，不能替代证据。

4. **让另一个 Agent 专门挑刺。** 它要追问：相比已有工作多了什么，基准公平吗，成本在哪里，结论能否外推，有没有反例和负面结果。

5. **最后写成读得下去的东西。** 说清楚发生了什么、为什么相关、证据多强、哪里不确定，以及接下来能读什么或试什么。重要变化放前面，原始链接保留。

这不需要一个无所不能的 Agent。找线索、整理证据、挑刺可以分开，最后由人决定是否相信、是否继续追。

我最近也在看 AI4Science Agent、PaperQA2 和 ARIS 这类工作流。它们提示我们，FrontPost 不必每天生成一份摘要，也可以持续跟踪一个问题：补充新来源、记录反例、定期复查，再交给人一份可继续追踪的进展。这只是借鉴思路，不代表 FrontPost 已经具备同类能力。[^5][^6][^7]

我还在想什么才算 AI-native。不是给旧报纸接一个大模型，每天生成一版“AI 早报”。那只是把编辑工作搬到后台，读者仍然被动接收一堆信息。

旧报纸的基本单位是“一期”，FrontPost 也许应该从一个问题或研究线索开始。新项目、反例和相关领域的来源可以持续补进来，用户也可以追问、收藏或跳过。

所以，问题不是让人每天看更多信息，而是把算力转化成更多人找得到、看得懂、用得上的前沿信息。不要只生成更多摘要和报告，而要帮助人找到线索、反例和值得继续追的问题。这可能是 FrontPost 更大的使命，但我们还需要继续验证。

## 接下来还可以怎么做

如果继续做下去，我想留下的是一张信源图谱和一套主张记录，而不是一个神秘总分。

根据用户的收藏、跳过、纠正和实际使用，FrontPost 可以逐步识别有效来源、可靠复现者和重复公关话术。同一个主张有新证据时，更新原记录，而不是再生成一篇孤立摘要。

精准不等于只推荐用户已经同意的内容。也要保留少量邻近领域、反常识和未上榜工作的空间。

刚入门的人不该先花几年混圈子，才知道前沿在哪里。传统社交媒体需要和推荐系统磨合；FrontPost 可以从一句话开始，再根据反馈调整来源。

我不想继续转发空洞的“重大突破”、名次崇拜和术语门槛。我想找的是准确、有趣、有新意，并且值得继续追踪的信息。FrontPost 不是要制造新的权威，而是让有价值的来源更容易被找到。

[^1]: Keller Jordan 的说明由 Varun Neal 在 [Muon in Modded NanoGPT](https://varunneal.github.io/essays/muon) 中引用。这里按其含义转述，不把个人观点当作普遍结论。
[^2]: Keller Jordan，[Muon: An optimizer for hidden layers in neural networks](https://kellerjordan.github.io/posts/muon/)；实现和后续资料见 [KellerJordan/Muon](https://github.com/KellerJordan/Muon)，公开训练竞速见 [KellerJordan/modded-nanogpt](https://github.com/KellerJordan/modded-nanogpt)。
[^3]: 相关项目见 [ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp)、[OpenFold](https://openfold.io/)、LAION 的 [DataComp 发布说明](https://laion.ai/blog/datacomp/) 与 [Re-LAION-5B 清理说明](https://laion.ai/blog/relaion-5b/)。开放项目同样会犯错，这里关注的是公开复现和纠错机制。
[^4]: Tony Harcup 与 Deirdre O’Neill 对新闻价值的研究列出了 surprise、magnitude、relevance 等常见因素，见 [What is News? News Values Revisited (Again)](https://www.tandfonline.com/doi/abs/10.1080/1461670X.2016.1150193)。
[^5]: Google Research，[Accelerating scientific breakthroughs with an AI co-scientist](https://research.google/blog/accelerating-scientific-breakthroughs-with-an-ai-co-scientist/)；Skarlinski 等，[Language agents achieve superhuman synthesis of scientific knowledge](https://arxiv.org/abs/2409.13740)；ARIS 项目仓库 [Auto-claude-code-research-in-sleep](https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep) 及其早期技术报告 [ARIS: Autonomous Research via Adversarial Multi-Agent Collaboration](https://arxiv.org/abs/2605.03042)。前两者有公开论文或评测，ARIS 的具体工作流和能力边界仍应按开源项目与报告理解，不代表 FrontPost 已具备同类能力。
[^6]: Google Research，[Towards a science of scaling agent systems: When and why agent systems work](https://research.google/blog/towards-a-science-of-scaling-agent-systems-when-and-why-agent-systems-work/)。该研究报告多 Agent 在可并行任务上收益明显，但在顺序任务上可能退化。
[^7]: Buchmann 等，[Evaluating and Guarding Citation Faithfulness in Agentic Scientific Synthesis](https://arxiv.org/abs/2607.20527)。论文报告同一批输出在不同验证器下测得的不受支持引用比例约为 3%–18%，说明验证器本身也需要校准。
