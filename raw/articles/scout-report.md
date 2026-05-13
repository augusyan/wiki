---
source_path: /data/yantianwei/000_AutoResearch/10_mmKG/artifacts/scout/scout_report.md
ingested: 2026-05-12
sha256: 0f8b238c37759768
---
# Scout Report: MMKG + LLM for Scientific Time-Series Forecasting

**Date:** 2026-05-07
**Source materials:** All files in `0_重要文献收集/` and `1_待复现代码/OKGLLM-main.zip`

---

## 1. What the Quest Is About

The research collection centers on the intersection of three areas:
1. **Multimodal Knowledge Graphs (MMKG)** — structured symbolic knowledge representations enriched with non-textual modalities
2. **Large Language Models (LLM)** — frozen or fine-tuned transformer-based models used as feature extractors or generators
3. **Scientific / domain-specific time-series forecasting** — particularly ocean science (Sea Surface Temperature)

The primary paper to reproduce and potentially extend is **OKG-LLM** (Yang et al., IEEE TKDE 2026).

---

## 2. Primary Paper: OKG-LLM

**Full title:** OKG-LLM: Aligning Ocean Knowledge Graph With Observation Data via LLMs for Global Sea Surface Temperature Prediction

**DOI:** 10.1109/TKDE.2026.3674110

**GitHub:** https://github.com/Neoyanghc/OKGLLM

### Core Idea

Traditional SST prediction models use only numerical observation data. OKG-LLM argues that marine physical knowledge (ocean currents, monsoons, sea basins, geographic regions) provides strong priors that purely data-driven methods fail to exploit. OKG-LLM bridges this gap by:
1. Constructing a domain Ocean Knowledge Graph (OKG) with 1,829 entities and 4,602 triples
2. Encoding OKG via TransE (structural) + LLM k-hop text serialization (semantic)
3. Using cross-attention to align each grid region's SST patch embedding with its KG neighborhood
4. Feeding aligned embeddings into a frozen GPT-2 as a sequence modeler

### Model Architecture

```
Input: X ∈ R^{N×T}  (N=1716 grid regions, T=96 weekly observations)

Module 1 - Time Series Encoding:
  RevIN normalization → patch split (P patches, L_p=patch_len) → MLP → E_ts ∈ R^{N×P×d_m}

Module 2 - OKG Encoding:
  Branch A: TransE pretrained struct embed → e_struct (frozen)
  Branch B: k-hop neighbor triples → linearize to text → LLM token embed → e_text
  Adapter: MLP([e_struct; e_text]) → E_kg ∈ R^{N×l×d}

Module 3 - Fine-Grained Alignment:
  query_i = LayerNorm(concat[e_region_i, e_ts_i])
  E_aligned = cross_attn(E_query, E_kg)

Module 4 - Prediction:
  Frozen LLM (GPT-2) → Transformer Decoder → Linear → Y_hat ∈ R^{N×tau}
```

### Reported Results (Best: patch_size=4, GPT-2 backbone)

| tau | MSE | MAE |
|---|---|---|
| 8 | 0.098 | 0.207 |
| 16 | 0.133 | 0.241 |
| 32 | 0.163 | 0.268 |

Improvement over TimeLLM (2nd best): 7.5–15.1% MSE reduction.

### Key Ablation Findings

- Removing KG encoding causes the largest performance drop (~largest contributor)
- Removing fine-grained alignment causes the second-largest drop
- Among KGE methods (TransE, ConvE, RotatE), results are similar
- GPT-2 outperforms Llama2-7B, DeepSeek-R1, OceanGPT as backbone (smaller = better; LLM provides sequence modeling, not world knowledge)

### Key Limitations (Future Work, per paper)

1. Static KG — no temporal events (typhoons, ENSO anomalies, marine heat waves)
2. Fixed region boundaries — no learnable or dynamic spatial assignment
3. Single variable (SST only) — no multi-variate ocean forecasting
4. No physics-informed loss — no heat conservation / fluid dynamics constraints
5. KG construction is semi-manual — not automatically updated from new literature
6. Limited downstream application (no event-level risk prediction)

---

## 3. Secondary Papers Read

### 3.1 mKG-RAG (MMKG for VQA)

**Task:** Knowledge-intensive Visual Question Answering  
**Method:** Construct multimodal KG from text+image; dual-stage retrieval (vector search → subgraph retrieval); query-aware multimodal retriever (QM-Retriever with contrastive + KL loss)  
**Results:** 36.3% on E-VQA, 40.5% on InfoSeek — >20% over zero-shot MLLM baseline  
**Relevance:** Demonstrates dual-stage retrieval over MMKG; visual relation modeling as first-class component; offline KG construction + online subgraph assembly pattern

### 3.2 Civil Aviation Maintenance KG+LLM

**Task:** Maintenance procedure retrieval and generation  
**Method:** GraphRAG + KG + LLM; Neo4j knowledge graph; TF-IDF weighted path re-ranking; local LLM for generation  
**Results:** Accuracy 3.81/5; quality answer rate 75%; expert recommendation rate 62.5%  
**Relevance:** Industrial application showing KG+LLM works in safety-critical verticals; re-ranking via path scoring; knowledge coverage gaps remain a challenge

### 3.3 MMKG Survey 2023-2026

Key trends identified:
- **VL-KGE** (WWW 2026): CLIP/BLIP-2 + structural KGE in shared space; supports single-modality inference
- **LBMKGC** (NeurIPS 2025): Stable Diffusion XL generates missing visual features for long-tail entities
- **RADD** (arXiv 2026): Two-stage MMKGC — dense retrieval + discrete diffusion re-ranking
- **TOFU** (arXiv 2026): Token-based foundation model for cross-graph zero-shot KGE
- **CMMKGR / MF-CKGE** (arXiv 2026): Continual learning for MMKG with multi-faceted semantic decoupling
- **MMGraphRAG / MegaRAG** (arXiv 2025-2026): Multimodal GraphRAG for complex document understanding
- **TRACE** (arXiv 2026): Experiential prior framework for multi-hop KGQA

---

## 4. Code Asset: OKGLLM-main.zip

**Archive date:** 2026-03-23 (after TKDE acceptance 2026-03-08)

### Confirmed Contents

| Component | File | Size | Status |
|---|---|---|---|
| Main model | `models/OKGLLM.py` | 20 KB | present |
| KG encoder | `models/kgmodel.py` | 6 KB | present |
| Training entry | `run_main.py` | 16 KB | present |
| SST dataset | `dataset/sst_noland_5x5_weekly_30.csv` | 50 MB | present |
| KG triples (train) | `data_provider/train.txt` | 249 KB | present |
| Entity dict | `data_provider/entities.dict` | 30 KB | present |
| Relation dict | `data_provider/relations.dict` | 84 bytes | present |
| KG currents data | `kg_data/currents.csv`, `currents_influence.csv` | 1.5 KB + 50 KB | present |
| KG monsoons data | `kg_data/monsoon.csv`, `monsoon_influence.csv` | 298 B + 2.9 KB | present |
| Pretrained KG emb | `pretrain_kg/emb.pth` | 3.6 MB | present |
| Run scripts | `scripts/OKGLLM_SST.sh` + `models/scripts/` (10 scripts) | - | present |
| TimeLLM reference | `models/TimeLLM.py` | 26 KB | present |

### Run Script (Primary)

```bash
# scripts/OKGLLM_SST.sh
model_name=OKGLLM
llm_model=GPT2
llm_dim=768
train_epochs=10
learning_rate=0.001
llama_layers=6
batch_size=256
d_model=32
d_ff=64

CUDA_VISIBLE_DEVICES=5 accelerate launch --mixed_precision bf16 --num_processes 1 run_main.py \
  --task_name long_term_forecast \
  --data SST \
  --seq_len 96 --label_len 16 --pred_len 32 \
  --enc_in 1716 --dec_in 1716 --c_out 1716 \
  --model OKGLLM --llm_model GPT2 --llm_dim 768
```

### Dependencies

Python 3.11, torch 2.2.2, accelerate 0.28.0, transformers 4.31.0, peft 0.4.0, deepspeed 0.14.0

---

## 5. Research Landscape Assessment

### Confirmed Research Niche

"KG + LLM + Time-Series Forecasting for Scientific Domains" remains a relatively uncrowded niche. OKG-LLM is the primary paper opening this direction in ocean science.

### Adjacent Positions

- TimeLLM / GPT4TS / UniTime: LLM for TS without KG — OKG-LLM's direct competitors
- GraphRAG / MMGraphRAG: MMKG + LLM for text/VQA — different modality target
- OceanGPT: domain-specific LLM for ocean but without KG alignment
- Physics-informed neural networks: physics constraints but no KG

### Differentiation Opportunities

1. **Temporal OKG** — add time-indexed triples (ENSO events, typhoon tracks) — directly extends OKG-LLM's acknowledged gap
2. **Multi-domain transfer** — apply same KG+LLM alignment to a new scientific domain (e.g., soil carbon, wildfire risk, epidemiology)
3. **Multimodal KG** — add satellite imagery as additional KG node attribute alongside text (connects to VL-KGE / MMKG survey themes)
4. **Foundation model for spatial KG-TS** — train a generalizable model across multiple domains using TOFU-style cross-graph transfer (higher ambition, higher risk)

---

## 6. Knowledge Gaps After Scout

- The survey PDF ("Towards LLM-Centric Multimodal Fusion") has not been read yet
- No experiments run yet — code untested on this machine
- GPU memory requirements unknown for batch_size=256, enc_in=1716
- Exact OKG construction pipeline (point-in-polygon scripts) may not be in the zip (only the output files are present)
- Whether the community has posted concurrent work building on OKG-LLM after March 2026 is unknown

---

## 7. Recommended Next Step

**Immediate:** Extract the zip, verify the environment, and run `scripts/OKGLLM_SST.sh` to confirm reproducibility before committing to any extension direction.

**Blocking decision for user:** Clarify the research goal — reproduce-only, extend OKG-LLM in a specific direction, or propose a new method. See `plan.md` Phase 2 for the four branch options.

**Recommended anchor:** `research-baseline`
