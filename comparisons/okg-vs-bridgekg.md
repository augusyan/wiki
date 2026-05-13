---
title: OKG-LLM vs BridgeKG-LLM Domain Comparison
created: 2026-05-12
updated: 2026-05-12
type: comparison
tags: [okg-llm, bridgekg-llm, domain-adaptation, kg-enhanced-forecasting]
sources: [raw/articles/bridgekg-quest-status.md, raw/papers/okg-llm-deep-read.md]
confidence: high
---

# OKG-LLM vs BridgeKG-LLM

## Domain Comparison

| Dimension | OKG-LLM (Ocean SST) | BridgeKG-LLM (Z24 Bridge) |
|-----------|---------------------|---------------------------|
| Prediction target | SST (°C) | Natural frequencies (Hz) |
| Channels | 1,716 grid cells | 5 modal frequencies |
| KG entities | 1,829 | 19 |
| KG triples | 4,602 | 28 |
| Relation types | 10+ (geophysical) | 6 (structural) |
| Temporal resolution | Weekly | Hourly |
| KG helps? | Yes (7.5-15.1% improvement) | No (slight degradation) |

## Why the Difference?

**Hypothesis 1: KG Density**
OKG's 4,602 triples provide rich contextual signal. BsKG's 28 triples may be below the threshold where KG conditioning becomes useful.

**Hypothesis 2: Domain Semantics**
Ocean KG captures complex geophysical interactions (currents, monsoons, basins). Bridge KG captures simple geometry (adjacent spans, similar cross-sections) — too simple to add value beyond the LLM's inherent pattern recognition.

**Hypothesis 3: Channel Count**
1,716 cells create a rich multivariate space where KG can disambiguate. 5 frequency modes may not benefit from spatial KG relationships.

## Implication

KG augmentation for LLM-based time series forecasting may require:
- Minimum KG density (hundreds to thousands of triples)
- Rich relational semantics beyond simple geometry
- Sufficient channel count for spatial conditioning

See also: [[okg-llm]], [[bridgekg-llm]], [[bskg]], [[concepts/kg-llm-time-series-forecasting]]
