# 全球 LLM / MaaS 区域需求代理指数：支撑材料简报

日期：2026-05-25  
用途：为博客中的“Regional LLM Demand Proxy 2026E”图提供可复核的依据。  
结论口径：这是多源代理指数，不是全球 token 消耗真值。

## 1. 为什么不能直接画真实 token 分布

目前没有公开来源披露 OpenAI、Google、Anthropic、阿里云百炼、腾讯混元、火山方舟、DeepSeek、MiniMax 等平台在全球各地区的可比 token 消耗总账。单一厂商数据会严重受模型可用地区、付费渠道、语言、合规和产品形态影响。

因此本简报采用“需求代理指数”：把公开可得的用户规模、GenAI 流量、Claude.ai 使用代理、AI 供给侧强度、AI readiness 与互联网基础设施合并判断。指数用于产品和架构推理，不能用于财务核算。

## 2. 指数结果

| 区域 | 指数 | 解释 |
| --- | ---: | --- |
| 东亚 East Asia | 100 | 中国 AI 产品用户规模和国产 MaaS/应用生态把 Anthropic 数据低估的部分补回来；日韩台在公开模型工具中也有可见使用。 |
| 北美 North America | 95 | 美国在 Claude.ai 使用、模型/API 供给、AI 投资和企业部署上都处于第一梯队。 |
| 欧洲 Europe | 72 | 使用代理和企业治理需求强，但供给侧集中度低于中美。 |
| 南亚 South Asia | 62 | 印度用户规模、开发者生态和公开 GenAI 流量强。 |
| 东南亚 Southeast Asia | 48 | 移动优先、增长快，但预算和基础设施集中度仍低于一线区域。 |
| 拉美 Latin America | 42 | 巴西、墨西哥等公开流量明显，但基础设施和模型供给较弱。 |
| 中东 MENA | 32 | 阿联酋、沙特、以色列等投入强，但区域总量较小。 |
| 非洲 Africa | 24 | 高增长潜力区，受连接、支付、企业预算约束。 |
| 大洋洲 Oceania | 18 | 澳新人均使用成熟，人口基数限制绝对量。 |

## 3. 证据栈

- 用户规模与产品采用：权重最高。CNNIC / 国务院英文摘要显示，截至 2025 年 12 月，中国 AI 产品用户规模达到 6.02 亿。这个量级足以说明用 Claude.ai country rows 直接估计东亚是不充分的。
- GenAI Web/App 流量：World Bank 2025 工作论文研究 ChatGPT 和各国本地 GenAI 工具访问，能补充“谁在实际用工具”的视角，尤其是中国本地工具流量和印度、巴西等大用户市场。
- 可见模型用量代理：Anthropic Economic Index 的 Claude.ai `usage_count` 可用于观察美国、欧洲、印度、日本、韩国、巴西等地区，但对中国和国产 MaaS 并不完整。
- 供给侧与基础设施：Stanford AI Index、Global AI Vibrancy、WIPO GenAI 专利报告说明中美、东亚和欧洲在模型、论文、专利、投资和人才上的差异；这些不是 token，但会影响 API 需求和网关产品形态。
- 部署准备度：Oxford Government AI Readiness 和 ITU 互联网指标用于约束中长期需求上限，帮助避免只看人口基数。

## 4. 对 LLMAPI 网关的含义

1. 区域路由不能只按延迟做。模型可用性、合规、价格、内容策略和对象存储位置会同时影响路由。
2. 东亚需求不能只从国际模型 API 可见流量推断。中国本地 MaaS、国产模型、企业私有化和多模态应用会形成独立高密度生态。
3. 增长区更需要低门槛与成本治理。南亚、东南亚、拉美和非洲的共性不是“没有需求”，而是价格、支付、网络、部署和运营门槛更敏感。
4. 多模态会把 token 需求变成资源需求。图片、音频、视频和长上下文任务需要 CDN/S3/对象存储、任务状态和审计证据，单纯 HTTP proxy 不够。
5. 安全和滥用防护适合云原生地内建，但不应喧宾夺主。IP 滥用、prompt injection、数据泄漏、模型 DoS、租户隔离和 PSI 类隐私增强能力可以成为平台能力，但早期产品仍应先解决调用、成本、日志、归档、合规默认项。

## 5. 主要来源

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
15. Kwon et al., *Efficient Memory Management for Large Language Model Serving with PagedAttention*, SOSP 2023. https://arxiv.org/abs/2309.06180
