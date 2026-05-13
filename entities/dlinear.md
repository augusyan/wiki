---
title: DLinear
created: 2026-05-08
updated: 2026-05-12
type: entity
tags: [dlinear, time-series, forecasting, baseline]
sources: [raw/articles/bridgekg-quest-plan.md, raw/articles/bridgekg-quest-status.md]
confidence: high
---

# DLinear

## Overview

DLinear (Zeng et al., AAAI 2023) is a simple yet strong linear baseline for long-term time series forecasting. It decomposes time series into trend and seasonal components, then applies single-layer linear models to each.

## Key Insight

Despite its simplicity, DLinear often outperforms complex Transformer-based models on standard benchmarks, arguing that many time series forecasting gains come from the decomposition rather than the architecture.

## Performance on Z24

On Z24 natural frequency prediction, DLinear served as a competitive linear baseline:
- tau=16: MSE=1.3477 (rank 6 of 6)
- tau=8: Competitive with KG-augmented variants
- tau=32: Competitive with KG-augmented variants

See also: [[bridgekg-llm]], [[comparisons/z24-experiment-results]]
