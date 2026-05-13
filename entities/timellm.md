---
title: TimeLLM
created: 2026-05-08
updated: 2026-05-12
type: entity
tags: [timellm, llm-patch-reprogramming, time-series, forecasting]
sources: [raw/articles/bridgekg-quest-plan.md, raw/articles/bridgekg-quest-status.md]
confidence: high
---

# TimeLLM

## Overview

TimeLLM (Jin et al., 2024) repurposes frozen LLMs for time series forecasting through patch reprogramming — converting time series patches into text-prototype representations that a frozen LLM can process, then projecting back to numerical outputs.

## Architecture

- Input time series → patch embedding
- Patch reprogramming → text prototype alignment
- Frozen LLM backbone (GPT-2, LLaMA, etc.)
- Output projection → forecast

## Performance on Z24

TimeLLM achieved the best MSE on Z24 frequency prediction at tau=16 (MSE=1.3051), outperforming all BridgeKG-LLM variants. This suggests that LLM patch reprogramming alone — without KG augmentation — is sufficient for this task.

| Horizon | TimeLLM MSE |
|---------|-------------|
| tau=8 | Best |
| tau=16 | 1.3051 (rank 1) |
| tau=32 | Competitive |

See also: [[bridgekg-llm]], [[okg-llm]], [[comparisons/z24-experiment-results]]
