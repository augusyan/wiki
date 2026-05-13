---
title: Z24 Bridge Benchmark
created: 2026-05-07
updated: 2026-05-12
type: entity
tags: [z24-bridge, shm, bridge-monitoring, benchmark, modal-analysis, natural-frequency]
sources: [raw/articles/bridgekg-quest-plan.md, raw/articles/selected-idea.md]
confidence: high
---

# Z24 Bridge Benchmark

## Overview

The Z24 Bridge was a post-tensioned concrete bridge in Switzerland, instrumented for a long-term structural health monitoring campaign before its demolition in 1998. It is an international standard benchmark for SHM research.

## Data

- **Raw data**: 10GB, 100Hz acceleration, 1 year healthy + 16 progressive damage scenarios
- **Source**: `/data/cold_data/data-z24.zip` (MAT format)
- **Processed**: 5653 hourly samples, 5 modal frequencies extracted via Welch PSD peak-picking
- **Output**: `dataset/z24_natural_freq_clean.csv`

## 5 Modal Frequencies

| Mode | Description | Typical Range |
|------|-------------|---------------|
| 1 | 1st bending | ~3.8-4.2 Hz |
| 2 | 1st torsional | ~4.8-5.2 Hz |
| 3 | 2nd bending | ~9.5-10.2 Hz |
| 4 | 2nd torsional | ~12.2-12.8 Hz |
| 5 | 3rd bending | ~17.1-17.8 Hz |

## 16 Damage Scenarios

Progressive damage applied during demolition: pier settlement, tilt, spalling, tendon rupture, etc. Used for damage detection evaluation via AUROC.

See also: [[bskg]], [[bridgekg-llm]]
