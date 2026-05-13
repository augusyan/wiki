---
title: Domain Adaptation in Time Series Forecasting
created: 2026-05-12
updated: 2026-05-12
type: concept
tags: [domain-adaptation, time-series, scientific-ml]
sources: [raw/articles/selected-idea.md, raw/articles/bridgekg-quest-status.md]
confidence: medium
---

# Domain Adaptation in Time Series Forecasting

## Definition

Adapting a time series forecasting model trained on one domain (source) to work on a different domain (target) with different data characteristics, modalities, or physical constraints.

## The BridgeKG-LLM Case

**Source**: OKG-LLM on NOAA SST (ocean temperature, 1,716 grid cells, rich geophysical KG)
**Target**: BridgeKG-LLM on Z24 (bridge natural frequencies, 5 modes, sparse structural KG)

### What Transferred
- LLM patch reprogramming (TimeLLM/OKGLLM architecture) worked well
- Code adaptation was straightforward (zero architecture changes)
- Training pipeline and hyperparameters were reusable

### What Didn't Transfer
- KG contribution: 4,602 triples (ocean) → 28 triples (bridge) — too sparse
- The cross-attention alignment mechanism may need richer KG signals
- Domain knowledge encoding strategy may need rethinking for small KGs

## Lessons Learned

1. LLM-based time series architectures are highly domain-portable
2. KG contribution is domain-dependent and requires sufficient KG density
3. Negative results (KG not helping) can be valuable contributions
4. Linear baselines (DLinear) remain competitive benchmarks

See also: [[bridgekg-llm]], [[concepts/kg-llm-time-series-forecasting]]
