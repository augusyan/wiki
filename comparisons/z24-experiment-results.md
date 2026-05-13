---
title: Z24 Experiment Results — Full Comparison
created: 2026-05-09
updated: 2026-05-12
type: comparison
tags: [experiment-result, z24-bridge, forecasting, ablation, negative-result]
sources: [raw/articles/bridgekg-quest-status.md, raw/articles/paper-draft.md]
confidence: high
---

# Z24 Bridge Experiment Results

## Main Results (tau=16)

| Rank | Model | Test MSE | Key Insight |
|------|-------|----------|-------------|
| 1 | **TimeLLM** | 1.3051 | Pure LLM best |
| 2 | BridgeKG-noKG | 1.3133 | Removing KG helps |
| 3 | BridgeKG-v2 | 1.3195 | Structure v2 marginal |
| 4 | BridgeKG-v2+TransE | 1.3220 | Pre-training ineffective |
| 5 | BridgeKG-v1 | 1.3219 | Original baseline |
| 6 | DLinear | 1.3477 | Linear baseline |

## By Horizon

### tau=8
| Model | MSE |
|-------|-----|
| TimeLLM | Best |
| BridgeKG | Competitive |
| BridgeKG-noKG | Competitive |
| DLinear | Competitive |

### tau=16
| Model | MSE |
|-------|-----|
| TimeLLM | 1.3051 |
| BridgeKG-noKG | 1.3133 |
| BridgeKG-v1 | 1.3219 |
| DLinear | 1.3477 |

### tau=32
| Model | MSE |
|-------|-----|
| BridgeKG | Competitive |
| BridgeKG-noKG | Competitive |
| DLinear | Competitive |

## Ablation Results

| Ablation | Effect | Finding |
|----------|--------|---------|
| Remove KG (noKG) | MSE improved | KG hurts, not helps |
| BsKG v1 → v2 | Negligible | Structure change insufficient |
| Add TransE pretrain | MSE worsened | Pre-training counterproductive |
| BridgeKG → TimeLLM | MSE improved | Architecture matters more than KG |

## Interpretation

1. LLM patch reprogramming alone is sufficient for Z24 frequency prediction
2. The BsKG (28 triples) is too sparse to provide useful conditioning
3. Adding more KG structure (v2, TransE) only adds noise
4. These are honest negative results that contribute to understanding KG-LLM's limitations

See also: [[bridgekg-llm]], [[okg-llm]], [[timellm]], [[dlinear]]
