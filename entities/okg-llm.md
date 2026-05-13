---
title: OKG-LLM
created: 2026-05-07
updated: 2026-05-12
type: entity
tags: [okg-llm, kg-enhanced-forecasting, ocean-sst, llm-patch-reprogramming]
sources: [raw/papers/okg-llm-deep-read.md, raw/articles/bridgekg-quest-brief.md]
confidence: high
---

# OKG-LLM

## Overview

OKG-LLM (Yang et al., TKDE 2026) is a three-module framework for knowledge-graph-enhanced time series forecasting, developed by researchers at Tongji University, Hong Kong PolyU, UIC, and Fudan University (authors: Hanchen Yang, Jiaqi Wang co-first; Wengen Li, Jihong Guan corresponding).

## Architecture

1. **Time-series patch encoding**: RevIN normalization + MLP embedding
2. **Domain Knowledge Graph encoding**: TransE structural embedding + LLM semantic embedding via k-hop neighbor linearization
3. **Cross-attention fine-grained alignment**: Feeds a frozen LLM backbone (GPT-2 default)

## Key Results

State-of-the-art SST prediction on NOAA OISST v2.1 (1,716 grid cells, weekly, 1991-present):
- 7.5-15.1% MSE improvement over TimeLLM
- Lower compute cost than TimeLLM
- KG used (OKG): 1,829 entities, 4,602 triples (regions, ocean currents, monsoons, basins)

## Reproduction Status

Successfully reproduced on our hardware (RTX PRO 5000 Blackwell 48GB):
| Horizon | Our MSE | Paper | Gap | Status |
|---------|---------|-------|-----|--------|
| tau=8 | 0.1042 | 0.098 | +6.4% | Pass |
| tau=16 | 0.1334 | 0.133 | +0.3% | Pass |
| tau=32 | 0.1631 | 0.163 | +0.1% | Pass |

6 bug fixes required for reproduction (hardcoded paths, PyTorch 2.6+ compatibility).

See also: [[bridgekg-llm]], [[bskg]]
