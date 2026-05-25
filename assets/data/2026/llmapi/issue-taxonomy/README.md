# LLM Gateway Issue Taxonomy Sample

This directory contains a reproducible appendix-style support package for the blog post section that discusses public issue evidence from LLM gateway and serving projects.

## Files

- `llm-gateway-issue-sample.csv`: 75 coded public GitHub issue observations.
- `llm-gateway-issue-sample-expanded.csv`: same sample, kept as a stable explicit dataset file.
- `llm-gateway-issue-category-counts.csv`: primary category counts.
- `llm-gateway-build-locus-counts.csv`: build-in-community / self-operated / mixed counts.
- `llm-gateway-l3-relevance-counts.csv`: relevance to L3 business asset layer.
- `llm-gateway-project-counts.csv`: project distribution.
- `llm-gateway-category-project-matrix.csv`: category by project matrix.
- `coding-codebook.csv`: compact coding schema.
- `sampling-notes.json`: retrieval and sampling notes.
- `llm-gateway-issue-appendix.md`: appendix narrative.
- `llm-gateway-issue-appendix.pdf`: PDF appendix.

## Sampling Frame

The sample was collected from public GitHub issue search pages on 2026-05-25. It covers gateway and adjacent serving projects including APIPark, LiteLLM, Higress, New API, Portkey, Bifrost, APISIX, Envoy AI Gateway, Helicone, vLLM, SGLang, and llama.cpp.

The sample is purposeful rather than probabilistic. It is intended to support qualitative product research and issue taxonomy, not to estimate population-level defect rates.

## Coding Unit

The coding unit is one public issue observation. Each row receives one `primary_category`; secondary matched dimensions are kept in `secondary_tags`.

`relevance_to_l3` does not refer to OSI Layer 3 networking. It estimates whether an issue touches the paper's L3 business asset and compliance layer: durable resource identifiers, object storage/CDN persistence, permissions, audit evidence, long-term logs, cost ledger, and policy enforcement.

## Limitations

The dataset uses public issue titles and list-page metadata; full issue bodies are not reproduced. GitHub issue traffic is shaped by project size, user base, maintainership, and reporting culture. Counts therefore indicate observed friction patterns in the sampled corpus, not market-wide frequency.
