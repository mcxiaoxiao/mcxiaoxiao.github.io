---
title: '为不确定性建立秩序：LLM API 网关的设计与实现'
date: 2026-05-24
permalink: /posts/llmapi-gateway
tags:
  - 技术
  - AI
  - 产品
  - 市场
---

## TLDR

LLM API 网关不是“把几个模型接口包一层”这么简单。对 AI-native 产品来说，它更像一层执行控制面：在模型、推理 aaS、对象存储、任务状态机、内容安全、成本统计和合规审计之间建立秩序。本文从我在 AI 漫画平台里自建 `llmapi` 模块的实践出发，讨论为什么通用聚合层还不够、媒体生成和多 agent 场景会怎样放大网关价值，以及以后面向国内小微团队的低门槛自托管 LLMAPI 网关可能长成什么样。

![标题图](/images/2026/llmapi/cover.png)

## 摘要

这篇文章有两条线索。

一条是工程线：我在开发某 AI 产品的 `llmapi` 模块时，面对多家 provider、多种模态、多种执行路径和越来越多的合规要求，最终选择自建网关，并逐步搭出一套包含任务状态机、资源持久化、key 运行时治理、日志审计和调用统计的系统。

另一条是商业线：LLM API 接入正在从“调模型”变成“管理不确定的智能执行”。模型能力、价格、限流、地区、内容安全、数据出境、审计留痕都会变化。对 AI-native 产品来说，网关不只是技术中间件，而是把推理 aaS、模型供应商、业务资产和合规证据接到一起的控制面。

我的结论很简单：通用 LLM 聚合层已经有价值，但在国内小微团队、媒体生成任务和合规友好自托管场景里，仍然存在一个没有被完全填上的位置。

***

## 一、这不是新问题：同构困境反复出现

每当一批新的后端服务涌现，总会出现同一个问题：N 个服务各有各的协议，业务系统不可能为每一个单独适配，于是需要一个中间层统一接入。

**ESB 时代**：企业内部的 ERP、CRM、OA、财务系统有不同协议和数据格式，Enterprise Service Bus 用 XML、SOAP、消息队列把它们串起来。它解决了连接问题，也带来了重量级配置和长期技术债。

**API 网关时代**：微服务之后，Kong、APISIX、Nginx Plus、AWS API Gateway 这一代基础设施把重点放到路由、认证、限流和观测。

**LLM API 网关时代**：现在的问题又变了一层。OpenAI、Anthropic、阿里云百炼、火山方舟、MiniMax、Google AI、各类推理 aaS 和聚合平台都在快速变化。文本、图像、视频、音频、CV 服务的协议不同；同步返回、流式返回、提交任务后轮询的生命周期也不同。LiteLLM、OpenRouter、Portkey、Higress 等工具已经说明这个市场真实存在，但它们的侧重点不同：有的偏 OpenAI 兼容代理，有的偏 provider routing，有的偏企业治理和 guardrails，有的偏云原生网关插件化。[^1][^2][^3][^4]

历史规律很清楚：新的后端生态爆发之后，中间层会从“能接入”走向“可运维”，再走向“可审计”。LLM API 这条线正在这个过程中。

***

## 二、市场现状：AI-native 产品需要新的控制面

市场上的 LLM 接入方案可以粗略分成四层：

| 层级 | 定位       | 典型形态                                                     | 核心价值                      |
| -- | -------- | -------------------------------------------------------- | ------------------------- |
| L0 | 模型与推理执行层 | OpenAI、Claude、Gemini、Qwen、DeepSeek、Llama、vLLM、SGLang、TGI | 产出 token、图片、视频、音频或分析结果    |
| L1 | API 聚合层  | LiteLLM、OpenRouter、One API、Evolink                       | 统一接口、模型路由、fallback、key 管理 |
| L2 | 网关治理层    | Portkey、Higress、Cloudflare AI Gateway、Kong AI Gateway、Envoy AI Gateway | 预算、限流、观测、guardrails、插件治理  |
| L3 | 业务资产与合规层 | 自建或深度定制                                                  | 任务状态机、资源入库、内容审核、审计证据、业务权限 |

AI 投入已经从“尝鲜”进入“规模化但仍不稳定”的阶段。Stanford AI Index 记录到生成式 AI 私人投资持续增长；McKinsey 2025 年调研也显示企业正在从试点走向 agentic AI 实验和局部扩展，但真正把价值吃下来的公司仍是少数。[^5][^6] Gartner 对 agentic AI 的判断更冷静：一边预测企业软件会快速加入 agentic 能力，一边提醒大量项目会因为 ROI、风险控制和产品包装不清而取消。[^7]

这对 LLMAPI 网关的启发是：未来的瓶颈不只是“有没有模型可用”，而是“能不能把模型调用变成可治理、可追踪、可计费、可复盘的业务执行”。推理 aaS 会继续降低算力和模型门槛，但它也会把 provider 波动、区域合规、价格策略和结果持久化问题留给上层系统。

模型层本身也在分化：闭源 API 模型继续提供最高上限和多模态产品化能力，OpenAI、Claude、Gemini 这类模型族持续强化工具调用、长上下文、音视频和 agent 能力。[^35][^36][^37] 开源或开放权重模型则让私有化、低成本和可控部署变得现实，Qwen、DeepSeek、Llama、Mistral 等生态正在快速成熟。[^38][^39][^40][^41] 与此同时，vLLM、SGLang、TensorRT-LLM、TGI、Ollama、llama.cpp 这类推理框架把自托管推理从“能跑”推向“可服务化”。[^29][^30][^31][^32][^33][^34] 这意味着 LLMAPI 网关不能只假设“上游是一个 OpenAI 兼容接口”，而要准备面对闭源 API、国产云、聚合商、自托管推理、离线模型和多模态专用模型的混合运行。

***

## 三、监管正在改变网关的职责

国内 AI 产品的合规要求不是一个单点动作，而是一条链。

底座是《网络安全法》《数据安全法》《个人信息保护法》。它们决定了数据安全、个人信息处理、重要数据和跨境流动的基本边界。[^8][^9][^10] 在 AI 应用层，《生成式人工智能服务管理暂行办法》要求提供生成式 AI 服务时关注安全、真实、合法、权益保护和生成内容标识；《互联网信息服务算法推荐管理规定》要求算法备案、内容治理、用户权益保护和算法透明；《互联网信息服务深度合成管理规定》覆盖文本、语音、图像、视频等深度合成服务，并要求对虚假信息风险、备案和标识进行管理。[^11][^12][^13] 2025 年发布并施行的《人工智能生成合成内容标识办法》进一步把显式标识、隐式标识、平台提示和元数据要求推到更具体的位置。[^14]

跨境调用也不能简单写成“能访问就能用”。《促进和规范数据跨境流动规定》在放宽部分场景的同时，仍保留了安全评估、个人信息出境标准合同、个人信息保护认证等制度衔接。[^15] 对一个国内 AI 产品来说，调用境外 provider 时，网关至少要知道：payload 里有没有个人信息，是否需要脱敏，能否路由到本地或零留存端点，日志中保留什么，保留多久，谁能访问。

国外监管的共同方向也类似。欧盟 AI Act 用风险分级和 GPAI 义务管理 AI 系统与通用模型；GDPR 对个人数据处理和自动化决策有长期约束。[^16][^17] 美国目前更偏分散治理：NIST AI RMF 是自愿性风险管理框架，FTC 则用消费者保护和反欺诈执法处理夸大 AI 能力、误导用户等问题，加州也已经通过 AI 生成内容透明度相关立法。[^18][^19][^20]

这些规定并不要求所有团队都立刻做一个复杂治理平台，但它们共同指向同一件事：AI-native 产品需要把“调用模型”变成“带证据链的执行”。网关层天然适合承接这件事，因为它位于 payload、provider、结果、用户和业务系统之间。

***

## 四、第三方方案的结构性边界

这里不是说现有方案不好。相反，它们证明了 LLM 网关市场已经成立。

LiteLLM 的优势在于 OpenAI 兼容接口、虚拟 key、预算、路由和成本追踪，适合快速把多个文本模型接成统一入口。[^1] OpenRouter 的 provider routing、fallback、参数匹配、数据留存偏好和 ZDR 路由，让“同一个模型背后有多个供应商”这件事更可控。[^2] Portkey 和 Higress 更靠近生产治理，强调 guardrails、fallback、负载均衡、预算、限流、语义缓存、观测和插件体系。[^3][^4] Cloudflare、Kong、Envoy 这类网关体系也在向 AI Gateway 演进，说明传统 API 网关厂商已经把模型流量当成新一代网关场景。[^21][^22][^23]

但这些方案通常解决的是“请求如何到模型”。媒体生成类产品还会遇到另一层问题：provider 返回的是临时 URL、base64、二进制、轮询任务结果或多子任务聚合结果。业务真正需要的不是临时链接，而是平台内稳定的 `resource_id`、对象存储 key、权限、审计记录和后续可编辑资产。

因此，通用 L1/L2 网关可以作为入口或参考，但对图像、视频、音频、CV 和 agent 工作流来说，业务侧仍需要一层 L3：把外部执行结果收口进自己的资产体系和合规证据链。

***

## 五、为什么我们选择自建

结合当前代码现实，自建的理由可以更准确地写成这样：

| 需求                 | 当前系统状态                                                   | 现成 L1/L2 是否足够         |
| ------------------ | -------------------------------------------------------- | --------------------- |
| `submit_poll` 生命周期 | 已实现平台 `task_id` 与 `provider_task_id` 的状态机、轮询、终态聚合        | 通常不够，需要业务层补           |
| 媒体结果资源化            | 已实现远程 URL/base64/bytes 到 S3 兼容存储和 `resources` 表          | 通常不管平台资产入库            |
| 多模态同构              | 已接入文本、图像、视频、音频、CV、视频分析等 adapter，但覆盖和编排仍在扩展               | 通常只覆盖部分模态             |
| 文本流式               | 已有 `stream_chat()` 直连路径，返回 OpenAI Chat Completions chunk | 通用方案可覆盖，但要接入本地审计      |
| key 运行时治理          | 已有 disabled、cooldown、inflight、task-key 绑定                | 通用方案有类似能力，但难贴业务       |
| 合规审计               | 已有 runtime、audit、payload、error 分层日志                      | 还缺内容审核策略、脱敏规则、留存策略产品化 |
| 动态能力注册             | `ModelTypeDefinition` 仍偏静态                               | 需要后续框架改造              |

所以自建不是因为市场上没有工具，而是因为我们的业务边界超过了“LLM API 代理”。我们需要的是：统一调用入口、provider 参数保真、异步任务生命周期、资源持久化、审计证据、统计口径和国内 provider 优先适配。

***

## 六、我们是怎么实现的

### 整体分层

```text
业务代码
    ↓
Client API          统一调用入口，业务只传 model_id + params
    ↓
Runtime Config      从 DB 加载模型实例、key、URL 等，Redis 缓存热状态
    ↓
Adapter             协议翻译、参数校验、provider 请求/响应映射、状态归一
    ↓
Task Runner         批任务提交、轮询、终态收敛、结果聚合
    ↓
Resource Service    下载 provider 临时文件、上传 S3、写库、产出 resource_id
    ↓
Logging / Audit     runtime / audit / payload / error 分层日志
```

### 参数分层：`__llmapi` 与 `__model`

调用参数拆成两块：

- `params.__llmapi`：平台统一语义，包含 `inputs`、`control`、`adapt`
- `params.__model`：provider 原生参数，由 adapter 保真处理

`adapt` 层处理“业务意图明确，但各家 provider 表达不一致”的参数。`negative_prompt` 就是典型例子：有的 provider 有原生字段，有的要合并进 prompt，有的不支持。adapter 按 provider 能力决定映射方式，调用方不需要知道细节。

### 两种批任务执行模式 + 一条流式路径

```text
request_response   一次请求直接返回结果，适合文本、同步图像、CV
submit_poll        提交任务拿 provider_task_id，之后轮询到终态，适合视频、音频、异步图像
stream_chat        文本流式直连路径，不进入批任务状态机，输出 ChatCompletionChunk
```

代码层面 `ExecutionMode` 只有 `request_response` 和 `submit_poll`。`stream_chat()` 是独立的文本流式入口，会在当前 API 进程里直接消费 provider 流，并在 llmapi 边界统一成 OpenAI Chat Completions chunk。

### 任务调度：TaskIQ + Redis + asyncio

批任务通过 TaskIQ 分发，底层使用 Redis `ListQueueBroker`。LLMAPI 有独立于主应用的 broker，worker 可以单独部署和扩容。系统支持两种 dispatch 模式：

- `taskiq`：任务进入 Redis 队列，由独立 worker 消费，适合生产环境
- `local`：用 `asyncio.create_task` 在当前进程内直接执行，适合开发调试

并发主要靠 `asyncio`。例如请求 8 张图而 provider 单次限制 4 张时，系统会拆成多个子请求并发提交，再聚合结果；`submit_poll` 的多个子任务轮询也可以并行推进。

### HTTP 与 SDK 边界

大多数 provider HTTP 请求走 `HTTPTool` 和共享的 `httpx.AsyncClient` 连接池，按 origin 复用客户端实例，配置连接数、keepalive、HTTP/2 和重试策略。文本流式模型如果是 OpenAI-compatible，也可以直接使用 `openai.AsyncOpenAI`；框架要求 adapter 在 llmapi 边界返回统一事件和 `ChatCompletionChunk`，而不是把 provider 私有流格式泄漏给调用方。

### 状态管理：Redis

Redis 承担三个角色：

- **任务热状态**：`BatchTaskState` 存 Redis，包含 `status / provider_task_id / progress / result_ref / resource_ids`
- **key 运行时状态**：`disabled / cooldown / inflight` 跨 worker 共享，提交后绑定 `task_id -> key_id`
- **运行时配置缓存**：模型配置从 PostgreSQL 加载后写 Redis，TTL 默认 300 秒

### 资源持久化：aioboto3 + S3 兼容对象存储

```text
provider 临时 URL / base64 / bytes
    → httpx 下载或云端 fetch
    → aioboto3 上传到 S3 兼容对象存储
    → 写入 resources 表
    → 返回平台 resource_id
```

当前代码面向 S3 兼容后端，既可以接 RustFS，也可以接 MinIO、七牛 S3 等实现。这个设计的关键不是某个存储品牌，而是业务结果不再停留在 provider 临时 URL 上。

![架构图](/images/2026/llmapi/distriinfra.png)

### 日志与审计

```text
logs/llmapi/{date}/
├── runtime/    运行时日志
├── audit/      任务生命周期事件
├── payload/    请求与结果 payload 摘要
└── error/      详细错误记录
```

每个关键节点记录 `request_id / trace_id / task_id / provider_task_id / key_id / latency_ms` 等信息。`submit_poll` 进度变化达到阈值时会单独写轮询事件，方便回放任务生命周期。

> 详细架构、执行链路和 adapter 开发规范见 [docs/llmapi](./docs/llmapi/README.md)。

***

## 七、还不够好的地方

**provider 变更管理还不系统。** 各家 provider 字段、状态码、限流策略和返回结构变化很快，目前主要靠人工跟进。更理想的方式是对 OpenAPI schema、官方文档、真实 smoke 结果做差异检测。

**真实 provider 回归还没有持续化。** 仓库里已经有 `real_env_smoke.py`、`real_dashscope_smoke.py`、`real_dashscope_official_smoke.py` 等脚本，但还没有稳定纳入 CI、定时任务和告警。

**模型能力注册仍偏静态。** `ModelTypeDefinition`、`input_types`、`parameter_schema`、`shared_input_schema` 主要由 adapter 代码声明。新增模型仍需要代码发布，后续应走向“代码 adapter + 数据库能力配置”的混合模式。

**非 LLM 模态已经开始同构，但还没有产品化。** 火山 CV 分割、全妙视频分析、音频、视频、图像类 adapter 已经进入框架，但内容审核、裁剪、打水印、OCR、人脸检测、字幕和 agent 编排还需要继续收口。

**合规策略还不是 policy-as-code。** 现在有审计日志和 payload 摘要，但内容安全审核节点、脱敏规则、数据留存期限、跨境路由规则、用户授权记录还需要配置化。

**统计和主链路仍有耦合。** 调用统计在终态路径写入，后续可以改为 Redis Streams、Kafka 或 ClickHouse 管道，把任务成功率、平均延迟、provider 错误分布、成本分布做成实时面板。

***

## 八、展望：推理 aaS 与 LLMAPI 网关会走向哪里

我更愿意把未来的 LLMAPI 网关理解成 AI-native 产品的控制面，而不是传统意义上的代理。

**第一，推理 aaS 会商品化，但调用治理不会商品化。** 模型和推理资源会越来越像云计算里的算力实例，价格下降、可替换性上升。但真正困难的是动态选择：哪次请求该走哪个 provider，是否需要零留存，是否允许境外端点，失败后能不能 fallback，结果是否已经落库，成本是否超预算。

**第二，网关的合约会从 API contract 升级为 execution contract。** 过去的 API 网关关心路径、鉴权和限流；LLMAPI 网关还要关心 prompt、输入资源、输出资源、任务状态、内容安全、审计证据和人类复核点。它管理的不是一次 HTTP 请求，而是一段不确定执行。

**第三，国内市场会需要“低门槛自托管 + 合规默认打开”的产品。** 大公司可以自建，中型团队会在开源和托管之间摇摆，小微团队最缺的是能在一台普通云服务器上跑起来的完整方案：Redis、PostgreSQL、S3 兼容对象存储、worker、Web 控制台、国内 provider adapter、合规日志和内容审核 hook。

**第四，agent 会放大网关价值。** 单次问答时代，网关只是模型代理；agent 时代，一次用户请求会触发搜索、读文件、生成图、生成视频、审核、裁剪、发布、回滚等多步动作。没有统一任务状态、资源引用和审计链路，agent 系统很快会变成难以复盘的黑箱。

**第五，真正的壁垒不在“兼容更多模型”，而在“把结果变成资产”。** 兼容 provider 很重要，但长期壁垒会出现在资源持久化、权限体系、业务状态机、合规证据、成本分析和用户工作流上。谁能把一次不确定的 AI 调用变成稳定、可追踪、可运营的业务对象，谁就更接近 AI-native 基础设施。

***

## 九、多 agent 与这件事的关系

我现在是研一新生，同时还要处理别的工作，实际开发越来越像“人 + 多 agent”的协作：一个 agent 查资料，一个 agent 写代码，一个 agent 补测试，一个 agent 做 review，最后还要有人把方向、边界和质量收回来。LangGraph、AutoGen、OpenAI Agents SDK、CrewAI、Semantic Kernel 这类框架都在解决 agent 编排、状态、工具调用和协作模式问题。[^24][^25][^26][^27][^28]

但多 agent 并不会让 LLMAPI 网关变得不重要，反而会放大它的重要性。agent 框架负责“谁来做、下一步做什么”，网关负责“调用哪个模型、用哪个 key、能不能出境、失败如何重试、结果是否落库、日志能否审计、成本算到哪里”。如果没有这一层，agent 越多，系统越容易出现不可追踪的模型调用、散落的临时资源、重复的 provider 适配和难以复盘的失败链路。

对个人开发者或研究生阶段的工程实践来说，这个关系更实际：时间有限，不能每个 provider、每个 agent、每个媒体任务都手写一遍接入逻辑。一个清晰的 LLMAPI 网关，可以把“新增模型”“新增 agent 工具”“新增真实 smoke”“新增合规策略”变成可委托、可检查、可复用的工程单元。

***

## 十、一个人能做什么

我是研一新生，考研备考那一年基本没怎么写代码，现在还要同时处理课程、项目和其他工作。重新上手这个项目时，一边补课一边做，很多坑反而让我看清楚了这个市场真实卡在哪里。

回头看，这个领域真正缺的不是另一个 LiteLLM 或另一个 Portkey，而是一个面向国内小微团队和个人开发者的、合规友好的、低门槛自托管 LLMAPI 网关。

它的初版可以很朴素：

- Docker Compose 启动，不强依赖 Kubernetes
- 文本、图像、视频、音频、CV 服务走同一套 `model_id + params`
- `submit_poll` 生命周期、资源持久化、key 管理、审计日志默认内置
- 国内 provider 优先适配，境外 provider 通过路由和脱敏策略接入
- 提供一个简单控制台，看 key、成本、错误率、任务状态和资源结果

这不是一个宏大的产品愿景，而是一个可以从工程实践里慢慢长出来的方向。AI-native 应用不会只需要模型，它们需要一层能把模型、资源、合规和业务动作编排在一起的基础设施。LLMAPI 网关正好站在这个位置。

***

![往者不可谏，来者犹可追](/images/2026/llmapi/life-paths-open-future.jpg)

*往者不可谏，来者犹可追！本科四年辛苦啦老己，毕业快乐🎓！*

## 参考资料

[^1]: [LiteLLM Documentation](https://docs.litellm.ai/)。LiteLLM 文档介绍其 Proxy Server、虚拟 key、预算、负载均衡和成本追踪等能力，适合作为 OpenAI-compatible 聚合层参考。

[^2]: [OpenRouter Provider Routing Documentation](https://openrouter.ai/docs/features/provider-routing)。OpenRouter 文档说明 provider routing、fallback、参数匹配、数据留存偏好和 ZDR 路由等机制。

[^3]: [Portkey AI Gateway Documentation](https://portkey.ai/docs/product/ai-gateway)。Portkey 文档列出 universal API、guardrails、fallback、负载均衡、预算、限流、缓存和观测能力。

[^4]: [Higress AI Gateway](https://higress.cn/en/ai-gateway)。Higress 官方页面介绍多模型统一路由、协议转换、语义缓存、token 级限流、内容安全、认证、观测、fallback 和插件化能力。

[^5]: [Stanford HAI, The 2025 AI Index Report](https://hai.stanford.edu/ai-index/2025-ai-index-report)。AI Index 对模型、投资、产业采用、政策等指标做年度追踪，其中经济章节记录生成式 AI 投资增长。

[^6]: [McKinsey, The State of AI: Global Survey 2025](https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-state-of-ai-how-organizations-are-rewiring-to-capture-value)。McKinsey 2025 年调研讨论企业 AI 采用、agentic AI 试验、规模化难点和组织转型。

[^7]: [Gartner, Agentic AI Projects Prediction, 2025](https://www.gartner.com/en/newsroom/press-releases/2025-06-25-gartner-predicts-over-40-percent-of-agentic-ai-projects-will-be-canceled-by-end-of-2027)。Gartner 预测大量 agentic AI 项目会因 ROI、风险控制和过度包装等原因被取消，同时企业软件中的 agentic 能力会快速增加。

[^8]: [《中华人民共和国网络安全法》](https://www.cac.gov.cn/2025-12/29/c_1768735112911946.htm)。网络安全法是国内网络运行、信息安全、违法信息处置和关键基础设施保护的重要基础。

[^9]: [《中华人民共和国数据安全法》](https://www.npc.gov.cn/npc/c2/c30834/202106/t20210610_311888.html)。数据安全法确立数据分类分级、重要数据、数据出境和数据处理安全等基础制度。

[^10]: [《中华人民共和国个人信息保护法》](https://www.npc.gov.cn/npc/c2597/c5854/bfflywwb/202311/t20231117_433007.html)。个人信息保护法规定个人信息处理、告知同意、敏感个人信息、自动化决策和跨境提供等规则。

[^11]: [《生成式人工智能服务管理暂行办法》](https://www.gov.cn/zhengce/zhengceku/202307/content_6891752.htm)。该办法自 2023 年 8 月 15 日起施行，是国内生成式 AI 服务治理的核心部门规章之一。

[^12]: [《互联网信息服务算法推荐管理规定》](https://www.gov.cn/zhengce/2022-01/04/content_5728941.htm)。该规定覆盖算法推荐服务的备案、透明度、内容治理、用户权益保护和反不正当竞争等要求。

[^13]: [《互联网信息服务深度合成管理规定》](https://www.gov.cn/zhengce/202310/content_6909368.htm)。该规定覆盖深度合成服务，强调标识、辟谣、备案和对虚假信息风险的治理。

[^14]: [《人工智能生成合成内容标识办法》](https://www.cac.gov.cn/2025-03/14/c_1743654684782215.htm)。该办法自 2025 年 9 月 1 日起施行，进一步规范 AI 生成合成内容的显式标识、隐式标识和服务协议提示。

[^15]: [《促进和规范数据跨境流动规定》](https://www.cac.gov.cn/2024-03/22/c_1712776612187994.htm)。该规定对数据出境安全评估、个人信息出境标准合同、个人信息保护认证和豁免场景做了衔接安排。

[^16]: [European Commission, General-purpose AI obligations under the AI Act](https://digital-strategy.ec.europa.eu/en/factpages/general-purpose-ai-obligations-under-ai-act)；另见 [EUR-Lex Regulation (EU) 2024/1689](https://eur-lex.europa.eu/eli/reg/2024/1689/oj)。欧盟 AI Act 建立风险分级监管框架，并对 GPAI 模型提供者设置透明度和风险管理义务。

[^17]: [EUR-Lex, GDPR Regulation (EU) 2016/679](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX%3A32016R0679)。GDPR 是欧盟个人数据保护基础法规，对个人数据处理和自动化决策有持续影响。

[^18]: [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)。NIST AI RMF 1.0 是美国重要的自愿性 AI 风险管理框架，2024 年还发布了生成式 AI Profile。

[^19]: [FTC, Operation AI Comply](https://www.ftc.gov/news-events/news/press-releases/2024/09/ftc-announces-crackdown-deceptive-ai-claims-schemes)。FTC 通过消费者保护和反欺诈执法处理夸大 AI 能力、误导用户和不公平行为。

[^20]: [California SB-942, California AI Transparency Act](https://leginfo.legislature.ca.gov/faces/billTextClient.xhtml?bill_id=202320240SB942)。该法案要求特定生成式 AI 服务提供者围绕 AI 生成内容提供透明度和披露机制。

[^21]: [Cloudflare AI Gateway Documentation](https://developers.cloudflare.com/ai-gateway/)。Cloudflare AI Gateway 提供模型请求观测、缓存、限流、日志和多 provider 接入能力。

[^22]: [Kong AI Gateway Documentation](https://developer.konghq.com/ai-gateway/)。Kong 将模型路由、prompt guard、认证、限流和 AI 插件纳入 API 网关体系。

[^23]: [Envoy AI Gateway](https://aigateway.envoyproxy.io/)。Envoy AI Gateway 是面向 LLM 和 agent 流量的 Envoy 网关项目，强调统一路由、策略和可观测性。

[^24]: [LangGraph Documentation](https://docs.langchain.com/oss/python/langgraph/overview)。LangGraph 面向长运行、有状态 agent 工作流，支持图式编排、持久化和 human-in-the-loop。

[^25]: [Microsoft AutoGen Documentation](https://microsoft.github.io/autogen/stable/)。AutoGen 是微软开源的多 agent 应用框架，强调 agent 协作、工具使用和事件驱动运行时。

[^26]: [OpenAI Agents Guide](https://platform.openai.com/docs/guides/agents)。OpenAI Agents 文档介绍 agent、tools、handoffs、guardrails 和 tracing 等构建模块。

[^27]: [CrewAI Documentation](https://docs.crewai.com/)。CrewAI 面向多 agent 协作和自动化工作流，强调 role、task、crew 和 flow 组织方式。

[^28]: [Microsoft Semantic Kernel Agents](https://learn.microsoft.com/en-us/semantic-kernel/frameworks/agent/)。Semantic Kernel 的 agent 框架提供 agent 定义、协作和工具编排能力。

[^29]: [vLLM Documentation](https://docs.vllm.ai/)。vLLM 是高吞吐 LLM serving 框架，常用于自托管开源模型和兼容 OpenAI API 的推理服务。

[^30]: [SGLang Documentation](https://docs.sglang.ai/)。SGLang 面向 LLM 和 VLM 的高性能 serving 与结构化生成场景。

[^31]: [NVIDIA TensorRT-LLM Documentation](https://nvidia.github.io/TensorRT-LLM/)。TensorRT-LLM 提供面向 NVIDIA GPU 的 LLM 优化、部署和推理加速能力。

[^32]: [Hugging Face Text Generation Inference Documentation](https://huggingface.co/docs/text-generation-inference/index)。TGI 是 Hugging Face 的生产级文本生成推理服务。

[^33]: [Ollama](https://github.com/ollama/ollama)。Ollama 提供本地运行和管理开源模型的工具链，适合个人开发和轻量私有化实验。

[^34]: [llama.cpp](https://github.com/ggml-org/llama.cpp)。llama.cpp 是本地和边缘设备运行 LLM 的重要开源项目，强调低依赖和多硬件后端。

[^35]: [OpenAI Models Documentation](https://platform.openai.com/docs/models)。OpenAI 模型文档列出 GPT、音频、图像等模型族及其能力边界。

[^36]: [Anthropic Claude Models Documentation](https://docs.anthropic.com/en/docs/about-claude/models/overview)。Anthropic 文档说明 Claude 模型族、能力、上下文窗口和使用建议。

[^37]: [Google Gemini API Models Documentation](https://ai.google.dev/gemini-api/docs/models)。Gemini API 文档列出 Google Gemini 模型族和多模态能力。

[^38]: [Qwen Documentation](https://qwen.readthedocs.io/en/latest/)。Qwen 文档覆盖阿里通义千问开源模型、部署、推理和应用示例。

[^39]: [DeepSeek API Documentation](https://api-docs.deepseek.com/)。DeepSeek 文档提供模型、API 调用、价格和接入说明。

[^40]: [Meta Llama Documentation](https://www.llama.com/docs/overview/)。Llama 官方文档介绍 Meta Llama 模型生态、部署和使用方式。

[^41]: [Mistral AI Models Documentation](https://docs.mistral.ai/getting-started/models/models_overview/)。Mistral AI 文档介绍其开源和商业模型族、能力边界和部署方式。

***


