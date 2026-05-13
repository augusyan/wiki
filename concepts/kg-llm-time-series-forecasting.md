---
title: KG-LLM Time Series Forecasting
created: 2026-05-12
updated: 2026-05-12
type: concept
tags: [kg-enhanced-forecasting, llm-patch-reprogramming, time-series]
sources: [raw/papers/okg-llm-deep-read.md, raw/articles/bridgekg-quest-status.md]
confidence: medium
contested: true
---

# KG-LLM Time Series Forecasting

## Definition

A paradigm that combines Knowledge Graphs (KG) with frozen Large Language Models for time series forecasting. The KG provides structured domain knowledge that conditions the LLM's patch reprogramming process.

## Core Mechanism

1. **Time series** → patched into tokens
2. **KG** → k-hop neighborhood linearized into text, TransE embeddings
3. **Cross-attention** → aligns time series patches with KG semantics
4. **Frozen LLM** → processes the aligned representation
5. **Output projection** → forecast values

## Current State of Knowledge

- **When it works**: OKG-LLM on SST (1,716 grid cells, rich KG with 4,602 triples) shows 7.5-15.1% MSE improvement over TimeLLM
- **When it doesn't**: BridgeKG-LLM on Z24 (5 frequencies, sparse KG with 28 triples) shows no benefit over pure LLM patch reprogramming

## Open Questions

1. Is there a minimum KG density/richness threshold for usefulness?
2. Does KG contribution scale with the number of time series channels?
3. Can dynamic/temporal KGs provide benefits where static structural KGs don't?
4. Is cross-attention the right mechanism, or would other fusion methods work better?

## Related Concepts

- [[concepts/llm-patch-reprogramming]]
- [[concepts/structural-health-monitoring]]
- [[concepts/domain-adaptation-in-time-series]]
