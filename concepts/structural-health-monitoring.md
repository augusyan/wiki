---
title: Structural Health Monitoring (SHM)
created: 2026-05-12
updated: 2026-05-12
type: concept
tags: [shm, bridge-monitoring, damage-detection, modal-analysis]
sources: [raw/articles/selected-idea.md, raw/articles/bridgekg-quest-brief.md]
confidence: medium
---

# Structural Health Monitoring (SHM)

## Definition

SHM is the process of implementing a damage detection strategy for engineering structures (bridges, buildings, aircraft, etc.) using sensor data to assess structural condition over time.

## Key Concepts

### Modal Analysis
Extracting natural frequencies, mode shapes, and damping ratios from vibration data. Changes in these modal parameters can indicate structural damage.

### Damage Detection
Using deviations from baseline behavior to detect anomalies:
- **Prediction residual**: difference between predicted and actual measurements
- **AUROC**: Area Under ROC Curve for binary classification (healthy vs damaged)

### Z24 Benchmark
International standard SHM benchmark with 1 year of healthy monitoring + 16 progressive damage scenarios.

## Why LLM + KG for SHM?

- SHM sensors produce multivariate time series (natural frequencies, accelerations)
- Bridge structural physics can be encoded as a KG (components, materials, geometry)
- LLM patch reprogramming has shown success on general time series forecasting

## Open Questions

- Does KG encoding of structural physics help prediction accuracy? (Current evidence: no, for small KGs)
- Can prediction residuals alone detect damage without explicit KG conditioning?
- How to incorporate temporal KG dynamics (crack propagation, material degradation)?

See also: [[z24-bridge]], [[bskg]], [[bridgekg-llm]]
