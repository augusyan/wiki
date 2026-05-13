---
title: Wiki Index
created: 2026-05-12
updated: 2026-05-12
type: concept
tags: [meta, index]
---

# Wiki Index

> Content catalog. Every wiki page listed under its type with a one-line summary.
> Read this first to find relevant pages for any query.
> Last updated: 2026-05-12 | Total pages: 9

## Entities

- [[okg-llm]] — KG-enhanced SST forecasting framework (TKDE 2026), 3-module frozen GPT-2 architecture
- [[bridgekg-llm]] — Adaptation of OKG-LLM to Z24 bridge SHM; KG found to not help
- [[bskg]] — Bridge Structural Knowledge Graph: 19 entities, 6 relations, 28 triples
- [[z24-bridge]] — International SHM benchmark: 5653 samples, 5 modal frequencies, 16 damage scenarios
- [[timellm]] — LLM patch reprogramming for time series; best performer on Z24
- [[dlinear]] — Simple linear decomposition baseline for time series forecasting

## Concepts

- [[concepts/kg-llm-time-series-forecasting]] — Paradigm combining KG + frozen LLM for time series; when it works and when it doesn't
- [[concepts/structural-health-monitoring]] — SHM fundamentals: modal analysis, damage detection, Z24 benchmark
- [[concepts/llm-patch-reprogramming]] — Technique for repurposing frozen LLMs for numerical time series
- [[concepts/domain-adaptation-in-time-series]] — Lessons from ocean SST → bridge SHM domain transfer

## Comparisons

- [[comparisons/z24-experiment-results]] — Full experiment results: 6 models, 3 horizons, ablation analysis
- [[comparisons/okg-vs-bridgekg]] — Why KG helps on SST but not on Z24: density, semantics, channel count
