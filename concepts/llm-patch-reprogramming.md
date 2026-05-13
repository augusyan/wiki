---
title: LLM Patch Reprogramming
created: 2026-05-12
updated: 2026-05-12
type: concept
tags: [llm-patch-reprogramming, time-series, forecasting]
sources: [raw/papers/okg-llm-deep-read.md, raw/articles/bridgekg-quest-status.md]
confidence: high
---

# LLM Patch Reprogramming

## Definition

A technique that repurposes frozen pre-trained Large Language Models for time series tasks by converting numerical patches into text-like representations the LLM can process, without fine-tuning the LLM itself.

## How It Works

1. **Patching**: Divide time series into overlapping patches
2. **Reprogramming**: Project patches into the LLM's embedding space via a learned linear layer
3. **LLM Processing**: Pass reprogrammed patches through frozen LLM layers
4. **Output Projection**: Map LLM outputs back to numerical predictions

## Key Insights

- Frozen LLMs contain useful representations even for non-text tasks
- Patch reprogramming is computationally efficient (no LLM fine-tuning)
- The technique works across diverse domains (electricity, weather, traffic, SST, bridge frequencies)
- Text prompts can optionally provide domain context (TimeLLM)

## Open Questions

- How much does the choice of LLM backbone matter?
- Can multi-modal LLMs (vision + text) provide additional benefits?
- Is the KG cross-attention mechanism complementary or redundant with patch reprogramming?

See also: [[timellm]], [[concepts/kg-llm-time-series-forecasting]]
