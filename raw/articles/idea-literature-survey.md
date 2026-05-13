---
source_path: /data/yantianwei/000_AutoResearch/10_mmKG/artifacts/idea/literature_survey.md
ingested: 2026-05-12
sha256: 602664aa07cb4b43
---
# Literature Survey: BridgeKG-LLM Idea Stage

**Survey date:** 2026-05-09
**Stage:** Idea — initial sweep
**Coverage floor:** >= 5 papers close to task/mechanism/failure mode

---

## 1. Survey Ledger

| Query | Source | Newly added papers | Remaining gaps |
|---|---|---|---|
| OKG-LLM full read | Local notes (`0_重要文献收集/OKG-LLM...精读.md`) | P1, P2 (OKG-LLM paper + ablation) | Bridge domain |
| Bridge KG + LLM decision-making | Local notes (`04_bridgeAgents/notes/`) | P3 (BMKG-DCoT) | Sensor TS prediction |
| MMKG survey 2023-2026 | Local notes (`多模态知识图谱进展调研.md`) | P4 (survey, covers 50+ works) | — |
| Z24 benchmark deep learning | Web search (2025-05-09) | P5 (Z24 damage detection DNN), P6 (GAN digital twin Z24) | Modal frequency prediction task |
| SHM natural frequency prediction LLM | Web search (2025-05-09) | P7 (TFT for masonry tower SHM), P8 (Transformer bridge condition) | LLM+KG specific |
| Bridge SHM sensor GNN spatial | Web search (2025-05-09) | P9 (TPS-GNN bridge health monitoring), P10 (GNN review for civil infra) | KG-augmented version |
| TimeLLM + bridge SHM | Web search (2025-05-09) | P11 (HAT anomaly detection SHM), P12 (unsupervised anomaly Z24) | None found for LLM+bridge TS |
| OKG-LLM domain adaptation | Web search (2025-05-09) | Confirms P1 (same paper arXiv version) | — |

---

## 2. Papers in Survey

### P1 — OKG-LLM (Primary Baseline)
**Title:** OKG-LLM: Aligning Ocean Knowledge Graph with Observation Data via LLMs for Global Sea Surface Temperature Prediction
**Authors:** Hanchen Yang, Jiaqi Wang, et al. (Tongji Univ., PolyU, UIC, Fudan)
**Venue:** IEEE TKDE Vol.38 No.5, May 2026 (arXiv:2508.00933)
**Relevance:** Direct baseline to replicate and extend. KG-LLM alignment paradigm for scientific TS.
**Key facts:** SST prediction, 1716 grid cells, GPT-2 frozen backbone, TransE KG embeddings, 7.5-15.1% MSE improvement over TimeLLM, 4,602 KG triples.
**Source:** Local detailed reading + GitHub code.

### P2 — TimeLLM
**Title:** Time-LLM: Time Series Forecasting by Reprogramming Large Language Models
**Authors:** Jin et al.
**Venue:** ICLR 2024
**Relevance:** Primary competitor to OKG-LLM; reprograms LLM without domain KG; OKG-LLM beats it by 7.5-15.1%.
**Key facts:** Patch reprogramming + text prototype tokens for TS patches; no structured domain KG; general-purpose approach.
**Source:** Cited in OKG-LLM reading; part of baseline comparison.

### P3 — BMKG-DCoT
**Title:** Knowledge graph-driven bridge maintenance decision-making via integrating large language models and chain-of-thought reasoning
**Authors:** Yuchen Wang, Wen Xiong, Yanjie Zhu, C.S. Cai (Southeast University)
**Venue:** Automation in Construction 181, 2026
**Relevance:** First paper combining bridge KG + LLM for a bridge engineering task (maintenance decisions). Shows that bridge KG (1165 entities, 1404 relations) can be combined with LLM for bridge reasoning (F1=0.9529). Does NOT do TS prediction.
**Key facts:** KG schema includes bridge, component, defect, maintenance relation types. CBR-style LLM agent with KG lookup. No TS sensor data involved.
**Source:** Local notes (`04_bridgeAgents/notes/Knowledge-graph-driven-bridge-maintenance...`).

### P4 — MMKG Survey 2023-2026
**Title:** 2023-2026 Multimodal Knowledge Graph Progress Survey (多模态知识图谱进展调研报告)
**Authors:** Internal survey note
**Venue:** Internal
**Relevance:** Comprehensive coverage of MMKG ecosystem; identifies RADD (discrete diffusion KGC), TOFU (foundation model for KG), continual KGE, GraphRAG etc.
**Key facts:** VL-KGE (WWW 2026), CMMKGR, MF-CKGE, LBMKGC (NeurIPS 2025), RADD retrieval-augmented discrete diffusion. None applied to SHM.
**Source:** Local notes.

### P5 — Z24 DNN Damage Detection
**Title:** Bridge Damage Identification Using Deep Neural Networks on Time-Frequency Signals Representation
**Authors:** (multiple; MDPI Sensors 2023)
**Venue:** MDPI Sensors 23(13), 2023
**Relevance:** Z24 benchmark dataset usage for multiclass damage classification via CNN on synchrosqueezing transform. Shows Z24 is valid for DL-based SHM.
**Key facts:** 16 sensor channels at 100 Hz, 33 accelerometers, multi-class damage scenarios. SST + CNN achieves high accuracy.
**Source:** Web search result.

### P6 — GAN Digital Twin Z24
**Title:** A generative adversarial network optimization method for damage detection and digital twinning by deep AI fault learning: Z24 Bridge SHM benchmark validation
**Authors:** (Springer Nature, 2025)
**Venue:** Structural and Multidisciplinary Optimization, 2025
**Relevance:** Latest (2025) high-profile paper on Z24 benchmark; unsupervised GAN approach for damage detection; validates Z24 as a live benchmark. Shows that Z24 remains highly active for evaluation.
**Key facts:** GAN trained on healthy state only; no prior damage info needed; works without labelled damage data.
**Source:** Web search result.

### P7 — TFT for Masonry Tower SHM
**Title:** Deep learning and structural health monitoring: Temporal Fusion Transformers for anomaly detection in masonry towers
**Authors:** (ScienceDirect / Mechanical Systems and Signal Processing, 2024)
**Venue:** Mechanical Systems and Signal Processing, 2024
**Relevance:** Closest existing work to our proposed task: trains a Transformer (TFT) to predict natural frequencies of a structure from environmental inputs (temperature, humidity), then flags deviations as anomalies. Does NOT use KG or LLM.
**Key facts:** TFT trained on natural frequency + environmental inputs. Environmental factors strongly improve prediction. Temperature is the dominant covariate. No structural KG encoding.
**Source:** Web search result.

### P8 — Transformer Bridge Condition Prediction
**Title:** A Transformer Architecture for Enhanced Bridge Condition Prediction
**Authors:** (MDPI Buildings, 2025)
**Venue:** Buildings 10(10), 2025
**Relevance:** NLP-inspired Transformer for bridge condition rating prediction (categorical output), not continuous TS prediction. Shows Transformer advantage for bridge data.
**Key facts:** Uses historical inspection records; condition rating treated as token; long-range temporal dependency modeled by self-attention.
**Source:** Web search result.

### P9 — TPS-GNN Bridge Health Monitoring
**Title:** TPS-GNN: Predictive model for bridge health monitoring data based on temporal periodicity and global spatial distribution characteristics
**Authors:** Yu Liu, Lianzhen Zhang, Jiyu Xin, Sijie Peng (2025)
**Venue:** Structural Health Monitoring (SAGE), 2025
**Relevance:** Most directly comparable to our proposed method for the bridge TS prediction task. Uses GNN with spatial sensor topology (multi-hop neighborhoods) + temporal periodicity encoding. Up to 78% improvement. Does NOT use LLM or KG.
**Key facts:** Addresses oversmoothing in bridge sensor GNNs; encodes daily/weekly/seasonal periodicity; multi-hop global spatial representations. Real bridge monitoring datasets used.
**Source:** Web search result.

### P10 — GNN Review for Civil Infrastructure O&M
**Title:** Graph Neural Networks for building and civil infrastructure operation and maintenance enhancement
**Authors:** Marasco et al. (ScienceDirect, Advanced Engineering Informatics, 2024)
**Venue:** Advanced Engineering Informatics, 2024
**Relevance:** Systematic review of 111 studies; shows GNNs used for anomaly detection, fault classification, forecasting in civil infra. Physics-Informed GNNs highlighted. No LLM, no KG.
**Key facts:** GNN + BIM semantic enrichment; heterogeneous GNN models discussed; digital twin direction.
**Source:** Web search result.

### P11 — HAT Sensor Anomaly Detection SHM
**Title:** Hierarchical Attention Transformer-Based Sensor Anomaly Detection in Structural Health Monitoring
**Authors:** (MDPI Sensors, August 2025)
**Venue:** Sensors 25(16), 2025
**Relevance:** State-of-the-art Transformer-based anomaly detection for bridge SHM, applied to cable-stayed bridge. 96.3% accuracy. No KG, no LLM.
**Key facts:** Hierarchical local/global Transformer; works with limited labeled data (20%); cable-stayed bridge real-world data.
**Source:** Web search result.

### P12 — Unsupervised Anomaly Detection Z24
**Title:** Unsupervised Learning-Based Anomaly Detection for Bridge Structural Health Monitoring: Identifying Deviations from Normal Structural Behaviour
**Authors:** (MDPI Sensors, January 2026)
**Venue:** Sensors 26(2), 2026
**Relevance:** Uses Z24 modal frequency data for anomaly detection (CDPF + EVT). Validates Z24 natural frequency TS as the evaluation signal.
**Key facts:** Purely data-driven; no structural knowledge used; shows Z24 frequency data as tractable for unsupervised learning.
**Source:** Web search result.

---

## 3. Coverage Assessment

### What is covered:
- OKG-LLM architecture and ablation (P1, P2)
- Bridge KG for maintenance decisions (P3)
- MMKG ecosystem (P4)
- Z24 as a valid DL benchmark (P5, P6, P12)
- TS prediction for SHM with deep learning (P7, P8)
- Sensor topology GNN for bridge monitoring (P9, P10)
- Anomaly detection for bridge SHM (P11, P12)

### Still missing:
- No paper combining KG + LLM + bridge sensor TS prediction (confirmed gap).
- No paper applying OKG-LLM-style alignment to any non-ocean physical domain.
- Temporal bridge KG (dynamic) — no prior work found.
- Multi-type sensor fusion (strain + acceleration + temperature) in one KG-LLM model — no prior work.

### Literature floor: PASSED (12 papers, 7 directly task-relevant)

---

## 4. Confirmed Research Gap

The intersection of **KG-augmented LLM + bridge sensor time-series prediction** has zero published papers as of 2026-05-09. The two closest neighbors are:
- OKG-LLM (P1): KG + LLM for scientific TS, ocean domain only
- TPS-GNN (P9): GNN + bridge sensor TS, no LLM or KG

BridgeKG-LLM would be the first work to close this gap.
