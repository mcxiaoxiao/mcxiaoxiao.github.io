# Appendix: Public Issue Sample for LLM Gateway Product Research

Date: 2026-05-25  
Corpus size: 75 public GitHub issue observations  
Scope: LLM gateway, AI gateway, API gateway with AI plugins, and adjacent LLM serving projects

## A. Research Purpose

The issue sample supports a product-research claim in the blog post: public LLM gateway friction is not limited to "calling more models"; recurring problems also appear around provider schema fidelity, streaming parity, deployment, logging, reliability, agent/tool state, security, and multimodal resource handling.

The sample does not prove that every product must implement a separate L3 layer. It supports a narrower claim: when a product involves media generation, long-running tasks, or agent/tool workflows, resource persistence and compliance evidence become a distinct concern that may sit above generic L1/L2 gateway functions.

## B. Sampling Frame

Issues were collected from public GitHub issue search pages on 2026-05-25. The project frame includes:

- APIPark, LiteLLM, Higress, New API, Portkey, Bifrost
- APISIX, Envoy AI Gateway, Helicone
- vLLM, SGLang, llama.cpp

Queries used recent issue listings and high-relevance keywords such as `stream`, `tool`, `deploy`, `log`, `openai`, `retry`, and `image`. Additional exact issues were retained when they were already cited in the article and were strongly relevant to serving or compatibility behavior.

## C. Coding Protocol

Coding unit: one issue observation.

Primary category is mutually exclusive:

| Code | Definition |
| --- | --- |
| `provider_adapter_schema` | Provider compatibility, model/channel configuration, OpenAI-compatible schema, parameter preservation. |
| `streaming_protocol_parity` | Streaming vs non-streaming behavior, SSE/chunk handling, Responses API deltas, reasoning/thinking fields. |
| `agent_context_state` | MCP, tool/function calling, agent context, parser/renderer, state offload, context compression. |
| `deployment_ops` | Offline install, Docker/Compose/Kubernetes/Helm, Gateway API compatibility, private-network deployment. |
| `logging_audit_cost` | Request log archive, audit traces, metrics, usage, cost calculation, billing and token accounting. |
| `reliability_routing_key` | Retry, fallback, timeout, latency, rate limits, key rotation/disablement, health checking. |
| `multimodal_resource` | Images, audio, video, files, URLs, base64, object storage and persistent resource handling. |
| `security_guardrails` | Auth, permission, tenant isolation, secrets, guardrails, prompt/tool safety and abuse prevention. |

Secondary tags are non-exclusive and preserve overlapping interpretations. `build_locus` marks whether a capability is more suitable for community build-in, platform self-operation, or a mixed approach. `relevance_to_l3` does not refer to OSI Layer 3; it estimates whether the issue touches the L3 business asset and compliance layer, including durable resources, object storage/CDN persistence, permissions, audit evidence, long-term logs, cost ledger, and policy enforcement.

## D. Aggregate Results

Primary categories:

| Category | Count | Share |
| --- | ---: | ---: |
| Provider adapter / schema | 18 | 24.0% |
| Deployment / ops | 12 | 16.0% |
| Agent / tool state | 11 | 14.7% |
| Logging / audit / cost | 9 | 12.0% |
| Streaming / protocol parity | 9 | 12.0% |
| Reliability / routing / key | 7 | 9.3% |
| Security / guardrails | 5 | 6.7% |
| Multimodal / resource | 4 | 5.3% |

Build locus:

| Locus | Count | Share |
| --- | ---: | ---: |
| Community build-in | 39 | 52.0% |
| Self-operated | 21 | 28.0% |
| Mixed | 15 | 20.0% |

L3 relevance:

| Relevance | Count | Share |
| --- | ---: | ---: |
| Medium | 34 | 45.3% |
| High | 29 | 38.7% |
| Low | 12 | 16.0% |

## E. Interpretation

The sample supports three cautious observations:

1. Provider adapter and schema fidelity is the largest class in this sample. This supports treating adapter tests, schema preservation, OpenAI-compatible edge cases, and smoke cases as community-buildable infrastructure.
2. Deployment, streaming parity, and agent/tool state are large enough to be considered first-class gateway concerns, not occasional edge cases.
3. Multimodal resource and security samples are numerically smaller but have higher L3 relevance. They often imply persistence, permission, audit, or policy behavior outside a pure HTTP proxy.

## F. Limits

This is a purposeful public issue sample, not a probability sample. Counts are influenced by project popularity, maintainer workflow, user reporting habits, and GitHub search ranking. The sample is suitable for appendix evidence and taxonomy-building, but not for estimating true defect incidence across the whole AI gateway market.

Full row-level data is stored in `llm-gateway-issue-sample.csv`.
