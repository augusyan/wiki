---
title: BridgeKG-LLM
created: 2026-05-09
updated: 2026-05-12
type: entity
tags: [bridgekg-llm, shm, bridge-monitoring, kg-enhanced-forecasting, domain-adaptation, negative-result]
sources: [raw/articles/bridgekg-quest-plan.md, raw/articles/bridgekg-quest-status.md, raw/articles/selected-idea.md]
confidence: high
contested: true
contradictions: [comparisons/z24-experiment-results]
---

# BridgeKG-LLM

## Overview

BridgeKG-LLM adapts the [[OKG-LLM]] framework from ocean SST prediction to structural health monitoring (SHM) — specifically, natural frequency prediction for the Z24 Bridge benchmark.

## Approach

- **Main task**: Multi-step prediction of 5 natural frequencies (tau=8/16/32 hours)
- **Secondary task**: Damage detection via prediction residual AUROC
- **KG**: BsKG (Bridge structural Knowledge Graph) — 19 entities, 6 relations, 28 triples
- **Data**: Z24 Bridge (5653 hourly samples, 5 modal frequencies)
- **Baselines**: DLinear, TimeLLM, BridgeKG-noKG, BridgeKG-v2, BridgeKG-v2+TransE

## Key Finding (Unexpected)

**KG did not help.** The core result at tau=16:

| Model | Test MSE |
|-------|----------|
| TimeLLM | 1.3051 |
| BridgeKG-noKG | 1.3133 |
| BridgeKG-v2 | 1.3195 |
| BridgeKG-v1 | 1.3219 |
| DLinear | 1.3477 |

Removing the KG (BridgeKG-noKG) performed better than with KG. LLM patch reprogramming alone was sufficient. BsKG v2 structural improvements and TransE pre-training showed no benefit.

## Implications

- KG contribution to LLM-based time series forecasting may be domain-dependent
- Ocean SST has rich geophysical KG relations; bridge structural KG (few entities, simple geometry) may be too sparse
- Future direction: dynamic temporal KG that encodes time-varying structural states

See also: [[okg-llm]], [[bskg]], [[z24-bridge]], [[comparisons/z24-experiment-results]]
