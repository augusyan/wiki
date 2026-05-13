---
source_path: /data/yantianwei/000_AutoResearch/10_mmKG/brief.md
ingested: 2026-05-12
sha256: ac5b61fe8da66384
---
# Quest Brief: MMKG + LLM for Domain-Specific Prediction

## Working Title

Knowledge Graph-Enhanced LLM Forecasting for Scientific Time-Series Data

## One-Line Summary

Build on OKG-LLM (TKDE 2026) to investigate whether the KG-LLM fine-grained alignment paradigm generalizes beyond ocean SST prediction, and to identify new contributions achievable by extending or improving the framework.

## Background

OKG-LLM (Yang et al., TKDE 2026) proposes a three-module framework:
1. Time-series patch encoding (RevIN + MLP)
2. Domain Knowledge Graph encoding (TransE structural embedding + LLM semantic embedding via k-hop neighbor linearization)
3. Cross-attention fine-grained alignment feeding a frozen LLM backbone (GPT-2 default)

The paper achieves state-of-the-art SST prediction on NOAA OISST v2.1 (1,716 grid cells, weekly, 1991–present), with 7.5–15.1% MSE improvement over TimeLLM at lower compute cost. The KG used (OKG) contains 1,829 entities and 4,602 triples spanning regions, ocean currents, monsoons, ocean basins, and sea areas.

## Stated Research Area

Multimodal Knowledge Graph (MMKG) + Large Language Models, with emphasis on:
- KG-augmented time series forecasting (KG + LLM + TSF)
- Domain knowledge alignment with numerical observation data
- Potential extension to other scientific or industrial domains

## Literature Coverage Already Collected

- Comprehensive MMKG survey (2023–2026), covering MMKGC, continual KGE, GraphRAG, temporal KG, entity alignment
- Deep read of OKG-LLM (the primary paper to reproduce)
- Deep read of mKG-RAG (MMKG for VQA / knowledge-intensive tasks)
- Civil aviation maintenance KG+LLM+GraphRAG system (industrial application)
- Survey PDF: "Towards LLM-Centric Multimodal Fusion" (not yet read in full)

## Code Assets

- `1_待复现代码/OKGLLM-main.zip`: Full OKG-LLM source code from GitHub (Neoyanghc/OKGLLM), archived 2026-03-23
  - Includes: NOAA SST dataset (50 MB CSV), pre-trained KG embeddings, TransE training data, run scripts, model code
  - Main model file: `models/OKGLLM.py`
  - Entry point: `run_main.py`
  - Primary run script: `scripts/OKGLLM_SST.sh`

## Intended Next Steps (Inferred)

The most likely research path, given the materials collected, is one of:
1. Reproduce OKG-LLM baseline results on SST as the starting point
2. Propose an extension (e.g., temporal KG, multi-variable prediction, new domain)
3. Develop a new method that addresses OKG-LLM's limitations

## Open Questions

- Is the goal to reproduce OKG-LLM and then extend, or to propose a fully new method?
- Which domain extension is intended (if any): marine, aviation, other?
- What is the target venue / submission deadline?
- Is GPU compute available on the current machine?

## Key Contacts / Attribution

OKG-LLM authors: Hanchen Yang, Jiaqi Wang (co-first); Wengen Li, Jihong Guan (corresponding)
Affiliations: Tongji University, Hong Kong PolyU, UIC, Fudan University
