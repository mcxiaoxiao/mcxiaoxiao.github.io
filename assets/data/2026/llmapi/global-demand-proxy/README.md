# Global LLM Demand Proxy 2026E / 数据说明

本目录为博客《为不确定性建立秩序：LLM API 网关的设计与实现》中的全球 LLM 需求图提供支撑材料。这里的数字不是全球 token 账单，也不是任何一家模型公司的收入或用量数据；它是一个用于产品分析和架构讨论的公开数据代理指数。

## Files

- `regional-llm-demand-proxy-2026.csv`：区域综合需求代理指数，0-100 归一化。
- `source-inventory.csv`：数据源、论文和报告清单。
- `llm-demand-proxy-source-report.md`：简版研究说明。
- `llm-demand-proxy-source-report.html`：排版版研究说明。
- `llm-demand-proxy-source-report.pdf`：同内容 PDF 版本，便于离线阅读。

## Method

公开世界中，OpenAI、Google、阿里、腾讯、字节、Anthropic 等模型和 MaaS 平台没有披露可横向比较的“按地区 token 消耗总账”。因此本文采用五类 proxy 做综合判断：

1. 用户规模与产品采用：例如 CNNIC 的中国 AI 产品用户规模、Microsoft AI Diffusion 的国家采用差异。
2. GenAI Web/App 流量：例如 World Bank 对 ChatGPT 和中国本地 AI 工具访问的跨国比较。
3. 可见模型用量代理：例如 Anthropic Economic Index 的 Claude.ai `usage_count`。
4. 供给侧与基础设施：例如 Stanford AI Index、Global AI Vibrancy、WIPO GenAI 专利格局。
5. 政策与部署准备度：例如 Oxford Government AI Readiness、ITU 互联网连接度。

## Interpretation

东亚被设为最高档，并不是因为 Anthropic 数据直接给出最高占比；恰恰相反，Anthropic 数据对中国大陆和国产 MaaS 覆盖不足。综合 CNNIC 的中国 AI 产品用户规模、World Bank 对中国本地 GenAI 工具流量的观察、WIPO/Stanford 对中国与东亚 AI 供给侧强度的记录，以及日韩台在 Claude/ChatGPT 公开代理中的可见使用，东亚更适合被视为全球 LLM/MaaS 需求的一线区域。

指数适合回答“LLMAPI 网关为什么需要区域路由、合规、成本和资源归档能力”，不适合回答“某地区真实消耗了多少 token”。
