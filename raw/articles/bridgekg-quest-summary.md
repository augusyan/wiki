---
source_path: /data/yantianwei/000_AutoResearch/10_mmKG/SUMMARY.md
ingested: 2026-05-12
sha256: 101fdf35110100f0
---
# Quest Summary

**Quest:** BridgeKG-LLM — 桥梁结构健康监测的知识图谱增强时序预测  
**Last updated:** 2026-05-09  
**Current stage:** `idea_complete` → ready for Experiment

---

## 一句话描述

将 OKG-LLM（海洋知识图谱 + 冻结 GPT-2，TKDE 2026）迁移到桥梁结构健康监测领域：构建桥梁结构知识图谱（BsKG），条件化 frozen LLM 对 Z24 大桥固有频率进行多步预测，并用预测残差实现损伤检测评估（AUROC）。

---

## 已完成进度

### Stage 1: Scout — 完成

| 产出 | 位置 |
|---|---|
| MMKG 全景调研 (2023-2026) | `0_重要文献收集/多模态知识图谱进展调研.md` |
| OKG-LLM 精读 | `0_重要文献收集/OKG-LLM...精读.md` |
| 桥梁 SHM 领域论文笔记 | `11_shm_damage/notes/`、`04_bridgeAgents/0_重要文献收集/notes/` |
| Scout 报告 | `artifacts/scout/scout_report.md` |

### Stage 2: Baseline Reproduction — 完成 ✅

**OKG-LLM on SST 复现结果（2026-05-08 12:09 → 2026-05-09 00:27）：**

| Horizon (τ) | 我们的 MSE | 论文目标 | 偏差 | 通过 |
|---|---|---|---|---|
| τ=8  | 0.1042 | 0.098 | +6.4% | ✓ |
| τ=16 | 0.1334 | 0.133 | +0.3% | ✓ |
| τ=32 | 0.1631 | 0.163 | +0.1% | ✓ |

- 无 NaN/Inf；三个 checkpoint 均已保存
- 环境：`lx-vllm-qwen-v1`，PyTorch 2.9.1，DGL 1.1.3，GPT2 在本地 `/data/cold_weights/gpt2_ms/`
- 代码目录：`baselines/local/okgllm_sst_original/OKGLLM-main/`
- 详细报告：`baselines/local/okgllm_sst_original/baseline_report.md`

**主要 bug fixes（6 处）：**
1. `run_main.py` — 硬编码 `/remote-home/` 路径 → `os.path` 相对路径
2. `models/OKGLLM.py` — GPT2 路径 → `$GPT2_MODEL_PATH` 环境变量
3. `models/kgmodel.py` — `torch.load(..., weights_only=False)`（PyTorch 2.6+ 兼容）
4. `data_provider/data_loader.py` — `external_data = None`（废弃变量）
5. `utils/tools.py` — `prompt_bank` 路径 → 相对路径
6. 脚本 — `accelerate launch` → `python -m accelerate.commands.launch`（shebang 损坏）

### Stage 3: Idea Generation — 完成 ✅

**选定方向：BridgeKG-LLM on Z24（C1 + C3）**

| 项目 | 内容 |
|---|---|
| 主任务 | Z24 大桥固有频率多步预测（τ=8/16/32 小时） |
| 次任务 | 利用预测残差进行 16 种渐进损伤场景的 AUROC 检测 |
| 数据 | Z24 Bridge（10GB，100Hz，1年健康 + 16损伤场景），`/data/cold_data/` |
| KG 设计 | BsKG：~50实体（传感器/截面/跨度），~200三元组，物理结构关系 |
| 模型改动 | 零架构改动；新增 Dataset_Z24 加载器 + BsKG 文件 |
| 训练时间 | 预计 < 30 分钟/次（5通道 vs SST的1716通道） |
| 基线对比 | DLinear、TimeLLM（无KG）、无KG消融、随机KG消融 |

**研究贡献：**
- 首个将 KG+冻结LLM 框架用于桥梁传感器时序预测的工作
- 提出物理结构知识图谱（BsKG）schema：sensor-component-span 三层实体
- 在国际标准 SHM benchmark（Z24）上验证，可连接损伤检测评估

**关键产出文件：**
- `artifacts/idea/candidates.md` — 5个候选方向打分
- `artifacts/idea/selected_idea.md` — 选定方向详细设计（含代码计划）
- `artifacts/idea/literature_survey.md` — 12篇相关文献调研
- `artifacts/idea/related_work.md` — 相关工作对比图
- `artifacts/idea/research_outline.md` — 论文提纲草稿

---

## 下一步：实验阶段

### 实验路线图（估计 4-6 周）

**Step 1（1-2周）：Z24 数据预处理**
- 解压 `/data/cold_data/data-z24.zip`（Z24ems1/2/3.zip，MAT 格式）
- 提取小时分辨率固有频率（5个模态，FDD 方法）
- 输出：`dataset/z24_natural_freq.csv`，shape (N_hours, 5)

**Step 2（1周）：BsKG 构建**
- 编写 `entities.dict`、`relations.dict`、`train.txt`（按 OKG-LLM 格式）
- 物理依据：模态→截面敏感性映射（已在 idea 文档中详细设计）
- 输出：`kg_data/bridge_z24/`

**Step 3（< 1天）：TransE 预训练**
- 用 `pretrain_kg/run_pretrain.sh` 对 BsKG 训练 TransE
- 输出：`pretrain_kg/emb_bridge_z24.pth`

**Step 4（2-3天）：Dataset_Z24 + 注册**
- 新增 `data_provider/data_loader_z24.py`
- 在 `data_factory.py` 注册 `data_dict['Z24'] = Dataset_Z24`

**Step 5（1天）：首次训练（Gate 1 proof-of-concept）**
- 成功标准：BridgeKG-LLM MSE < DLinear MSE（至少一个 horizon）

**Step 6（1周）：全量实验 + 消融**
- 所有基线对比 + 损伤检测 AUROC 计算

---

## 关键数据路径

| 数据 | 路径 |
|---|---|
| Z24 Bridge 原始数据 | `/data/cold_data/data-z24.zip` 或类似路径 |
| bridge3.sql | `/data/cold_data/bridge3.sql` |
| GPT2 权重 | `/data/cold_weights/gpt2_ms/openai-community/gpt2/` |
| OKG-LLM 代码 | `baselines/local/okgllm_sst_original/OKGLLM-main/` |

---

## 计算环境

| 项目 | 值 |
|---|---|
| GPU | NVIDIA RTX PRO 5000 Blackwell（48GB，GPU 0 only） |
| Conda env | `lx-vllm-qwen-v1` |
| Python | 3.10，PyTorch 2.9.1，Transformers 4.57.6，DGL 1.1.3 |
| API（LLM调用） | SiliconFlow（Qwen/DeepSeek 系列） |
