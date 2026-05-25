# 全球 LLM / MaaS 区域需求代理指数：支撑材料简报

日期：2026-05-25<br>
用途：为博客中的 Regional LLM Demand Proxy 图提供可复核的定义、来源和限制。<br>
结论口径：这是多源代理指数，不是全球 token 消耗真值。

## 1. 研究问题与约束

公开来源目前没有披露 OpenAI、Google、Anthropic、阿里云百炼、腾讯混元、火山方舟、DeepSeek、MiniMax 等平台按地区可比的 token 消耗总账。单一厂商数据还会受服务可用地区、付费渠道、语言、企业采购、合规策略和产品形态影响。

因此本文定义一个区域需求代理指数，用来近似比较不同区域的 LLM / MaaS 需求强度。它适合产品和基础设施讨论，不适合市场份额、收入、账单或真实 token 量核算。

## 2. 指数定义

对区域 r，定义 Regional LLM Demand Proxy Index：

```text
RLDPI_r = 0.30 A_r + 0.25 T_r + 0.15 C_r + 0.15 S_r + 0.10 G_r + 0.05 Q_r
```

其中：

- A_r：Adoption，AI 产品用户规模与组织采用度。
- T_r：Traffic，GenAI Web/App 使用流量与访问强度。
- C_r：Claude visible usage，Anthropic Economic Index 中 Claude.ai `usage_count` 的可见使用代理。
- S_r：Supply and infrastructure，模型/API 供给、AI 投资、专利、数据中心和高性能推理生态。
- G_r：Governance/readiness，AI readiness、监管与部署准备度、互联网连接基础。
- Q_r：Qualitative correction，针对公开代理数据覆盖盲区的明示校正项。

各变量先按 0-100 归一化或分档评分，再按权重加总。Q_r 只用于处理公开数据明显缺口，例如 Claude.ai 国家数据不能完整代表中国大陆和国产 MaaS 生态。

## 3. 指数结果

| 区域 | 指数 | 解释 |
| --- | ---: | --- |
| 东亚 East Asia | 100 | 中国 AI 产品用户规模、国产 MaaS / 应用生态、日韩台公开模型工具使用，以及 WIPO/Stanford 供给侧指标共同支撑最高档。 |
| 北美 North America | 95 | 美国在 Claude.ai 使用、模型/API 供给、AI 投资、企业部署和数据中心能力上处于第一梯队。 |
| 欧洲 Europe | 72 | 使用代理和企业治理需求强，但供给侧集中度低于中美。 |
| 南亚 South Asia | 62 | 印度用户规模、开发者生态和公开 GenAI 流量强。 |
| 东南亚 Southeast Asia | 48 | 移动优先、增长快，但预算和基础设施集中度仍低于一线区域。 |
| 拉美 Latin America | 42 | 巴西、墨西哥等公开流量明显，但基础设施和模型供给较弱。 |
| 中东 MENA | 32 | 阿联酋、沙特、以色列等投入强，但区域总量较小。 |
| 非洲 Africa | 24 | 高增长潜力区，受连接、支付、企业预算约束。 |
| 大洋洲 Oceania | 18 | 澳新人均使用成熟，人口基数限制绝对量。 |

## 4. 证据栈

- 用户规模与产品采用：CNNIC / 国务院英文摘要显示，截至 2025 年 12 月，中国 AI 产品用户规模达到 6.02 亿；Microsoft AI Diffusion 用于补充国家采用差异。
- GenAI Web/App 流量：World Bank 2025 工作论文研究 ChatGPT 与各国本地 GenAI 工具访问，补充“谁在实际用工具”的视角。
- 可见模型用量代理：Anthropic Economic Index 的 Claude.ai `usage_count` 可观察美国、欧洲、印度、日本、韩国、巴西等地区，但对中国和国产 MaaS 不完整。
- 供给侧与基础设施：Stanford AI Index / Vibrancy、WIPO GenAI 专利、Oxford AI readiness、ITU 连接度用于约束中长期需求上限和基础设施条件。

## 5. 对 LLMAPI 网关的含义

1. 区域路由不能只按延迟做；模型可用性、合规、价格、内容策略和对象存储位置会同时影响路由。
2. 东亚需求不能只从国际模型 API 可见流量推断；中国本地 MaaS、国产模型、企业私有化和多模态应用会形成独立高密度生态。
3. 增长区更需要低门槛与成本治理；南亚、东南亚、拉美和非洲对价格、支付、网络、部署和运营门槛更敏感。
4. 多模态会把 token 需求变成资源需求；图片、音频、视频和长上下文任务需要 CDN/S3/对象存储、任务状态和审计证据。
5. 安全和滥用防护适合云原生内建，但早期产品仍应先解决调用、成本、日志、归档和合规默认项。

## 6. 局限

该指数是公开资料代理模型。它没有访问任何厂商内部 token 账单，也没有把国家级数据精确拆成企业 API、消费级 App、开发者工具和私有化部署。指数数值应理解为“相对需求强度”，而不是“市场份额”。

## 7. 主要来源

1. Anthropic, *Economic Index Dataset*, 2025. https://huggingface.co/datasets/Anthropic/EconomicIndex
2. Anthropic, *Economic Index*, 2025. https://www.anthropic.com/economic-index
3. World Bank, *Who on Earth Is Using Generative AI? Global Trends and Shifts in 2025*, 2025. https://documents.worldbank.org/en/publication/documents-reports/documentdetail/099856110152535288
4. World Bank, *GenAI Traffic Reproducibility Package*, 2025. https://reproducibility.worldbank.org/catalog/332
5. Microsoft AI Economy Institute, *Global AI Adoption 2025 / AI Diffusion Report*, 2025. https://www.microsoft.com/en-us/corporate-responsibility/topics/AI-Economy-Institute/reports/Global-AI-Adoption-2025/
6. CNNIC / State Council, *57th Statistical Report on China's Internet Development*, 2026. https://english.www.gov.cn/archive/statistics/202602/05/content_WS698442cac6d00ca5f9a08edc.html
7. WIPO, *Generative AI Patent Landscape Report*, 2024. https://www.wipo.int/web-publications/patent-landscape-report-generative-artificial-intelligence-genai/en/
8. Stanford HAI, *2025 AI Index Report*, 2025. https://hai.stanford.edu/ai-index/2025-ai-index-report
9. Stanford AI Index, *Global AI Vibrancy Tool*, 2025. https://aiindex.stanford.edu/vibrancy/
10. Oxford Insights, *Government AI Readiness Index*, 2024. https://oxfordinsights.com/ai-readiness/ai-readiness-index/
11. International Telecommunication Union, *Facts and Figures 2025*, 2025. https://www.itu.int/itu-d/reports/statistics/facts-figures-2025/
12. McKinsey, *The State of AI: How Organizations Are Rewiring to Capture Value*, 2025. https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-state-of-ai
13. Chen et al., *FrugalGPT: How to Use Large Language Models While Reducing Cost and Improving Performance*, 2023. https://arxiv.org/abs/2305.05176
14. Ong et al., *RouteLLM: Learning to Route LLMs with Preference Data*, 2024. https://arxiv.org/abs/2406.18665
15. Kwon et al., *Efficient Memory Management for LLM Serving with PagedAttention*, SOSP 2023. https://arxiv.org/abs/2309.06180
