---
source_path: /data/yantianwei/000_AutoResearch/10_mmKG/plan.md
ingested: 2026-05-12
sha256: 35b045e643efa644
---
# Quest Plan

## Overview

BridgeKG-LLM: 将 OKG-LLM 范式从海洋温度预测（SST）迁移到结构健康监测（Z24 桥梁固有频率预测），并扩展至损伤检测任务。

## Phase 0: Environment Setup ✅

- [x] Extract OKGLLM-main.zip to a working directory
- [x] Verify Python/CUDA environment (Python 3.10, torch 2.9.1, transformers 4.57)
- [x] Confirm GPU availability (NVIDIA RTX PRO 5000 Blackwell 48GB)
- [x] GPT-2 weights loaded via env var `GPT2_MODEL_PATH`
- [x] Z24 dataset: `dataset/z24_natural_freq_clean.csv` (5653 rows, 5 modes)
- [x] BsKG: `kg_data/bridge_z24/` (19 entities, 6 relations, 28 triples)

## Phase 1: Baseline Reproduction ✅

Goal: reproduce OKG-LLM main results on NOAA SST dataset.

Target metrics from paper (GPT-2 backbone, patch_len=4):
- tau=8: MSE=0.098, MAE=0.207
- tau=16: MSE=0.133, MAE=0.241
- tau=32: MSE=0.163, MAE=0.268

Steps:
- [x] Run `bash scripts/OKGLLM_SST.sh` (tau=32)
- [x] Run tau=8 and tau=16 variants
- [x] Record actual results; compare to paper numbers

Acceptance: MSE within 10% of paper → **PASSED** (max +6.4%)

## Phase 2: Domain Extension → BridgeKG-LLM ✅

### Step 2.1: Z24 Data Extraction ✅
- Parse `.aaa` ASCII acceleration files
- Welch PSD peak-picking → 5 modal frequencies per setup
- 5653 valid samples, hourly, standardized

### Step 2.2: BsKG Construction ✅
- Entities: mode_1_freq…mode_5_freq, cross-sections, spans, piers, materials, sensors
- Relations: measured_at, part_of_span, adjacent_to, structurally_similar_to, influenced_by, material_is
- 28 triples encoding structural physics

### Step 2.3: Code Adaptation ✅
- `Dataset_SST` 复用（同 CSV 格式）
- `KGDataset` 支持 configurable `entity_prefix`
- `run_main.py` 条件配置 KG 路径
- GPT2 路径改为 env var 加载

### Step 2.4: Gate 1 Proof-of-Concept ✅
- BridgeKG-LLM τ=8/16/32 vs DLinear τ=8/16/32 vs TimeLLM τ=8/16/32
- **Gate 1 通过**: BridgeKG < DLinear at τ=16 (-3.2%) and τ=32 (-1.2%)
- TimeLLM τ=16 补充运行中（后台）

## Phase 3: Ablation & Analysis（NEXT）

### Step 3.1: Ablation Studies
- [ ] BridgeKG-LLM **without KG** (ablation: 去掉 kg_embeddings 和 batch_descriptions)
- [ ] BridgeKG-LLM **without text prompt** (ablation: 仅保留 KG 嵌入)
- [ ] BridgeKG-LLM **random KG** (ablation: 随机打乱 triple 关系)

### Step 3.2: Damage Detection (Secondary Task)
- [ ] 利用预测残差区分 healthy vs damaged 状态
- [ ] 16 种渐进损伤场景 → AUROC 评估
- [ ] 对比 BridgeKG vs TimeLLM vs DLinear 的异常检测能力

### Step 3.3: Result Compilation
- [ ] 汇总所有实验结果表格（MSE/MAE per τ per model）
- [ ] 可视化：预测曲线 vs 真实值、残差分布、t-SNE embedding
- [ ] 撰写实验报告

## Phase 4: Paper Writing

- [ ] 论文大纲（Introduction → Related Work → Method → Experiments → Conclusion）
- [ ] 撰写 Method 部分（BridgeKG-LLM 架构 + BsKG 设计）
- [ ] 撰写 Experiments 部分（主任务 + 次任务结果）
- [ ] 生成图表

## Open Dependencies

- TimeLLM τ=16 结果待补充（后台运行中）
- Ablation 实验待执行
- 损伤检测 AUROC 评估待执行
