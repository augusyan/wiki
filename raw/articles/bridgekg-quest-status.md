---
source_path: /data/yantianwei/000_AutoResearch/10_mmKG/status.md
ingested: 2026-05-12
sha256: 8435b1048924ec9b
---
# Quest Status

## Current Stage

`paper_drafting` — 实验阶段结束，论文初稿撰写中。核心发现：KG 在 Z24 频率预测任务中未能带来正面贡献，LLM patch reprogramming 本身已足够强。

---

## 已完成项目（可信）

| 资产 | 位置 | 状态 |
|---|---|---|
| MMKG 文献调研 | `0_重要文献收集/` | ✅ 完成 |
| OKG-LLM baseline 复现 | `baselines/local/okgllm_sst_original/` | ✅ 完成（±10%以内） |
| Z24 数据提取 | `dataset/z24_natural_freq_clean.csv` | ✅ 完成（5653 rows × 5 modes） |
| BsKG v1/v2 构建 | `kg_data/bridge_z24/` | ✅ 完成 |
| 全量实验 | `logs/*.log` | ✅ 完成（9主实验 + 3消融 + 1预训练） |
| 消融分析 | 见 RESULTS.md | ✅ 完成 |
| 论文初稿 | `paper/main.md` | ✅ 完成 |

---

## 全量实验结果（τ=16）

| 排名 | 模型 | Test MSE | 关键结论 |
|---|---|---|---|
| 1 | **TimeLLM** | 1.3051 | 纯 LLM 最佳 |
| 2 | BridgeKG-noKG | 1.3133 | 去掉 KG 反而更好 |
| 3 | BridgeKG-v2 | 1.3195 | 结构改进微弱有效 |
| 4 | BridgeKG-v2+TransE | 1.3220 | 预训练完全无效 |
| 5 | BridgeKG-v1 | 1.3219 | 原始基线 |
| 6 | DLinear | 1.3477 | 线性基线 |

**核心结论**: KG 在当前设计下对 Z24 频率预测无正面贡献。

---

## 论文定位

**标题方向**: LLM-based Time Series Forecasting for Structural Health Monitoring: A Systematic Evaluation with Knowledge Graph Ablations

**核心贡献**:
1. 首次将 LLM 时序重编程系统应用于 SHM 领域
2. 构建了首个桥梁结构知识图谱 (BsKG)
3. 诚实的消融分析揭示 KG 局限性
4. 提出动态时序 KG 的未来方向

---

## Stage History

| Date | Stage | Event |
|---|---|---|
| before 2026-05-07 | pre-quest | 文献收集；OKG-LLM 代码下载 |
| 2026-05-07 | scout_complete | Intake audit |
| 2026-05-08 | baseline_running | 路径修复；pipeline 验证 |
| 2026-05-09 | baseline_complete | OKG-LLM 复现通过 |
| 2026-05-09 | gate1_complete | Gate 1 POC 验证（意外负面结果） |
| 2026-05-09 | ablation_complete | BsKG v2 + TransE 预训练均无效 |
| 2026-05-09 | paper_drafting | 论文初稿完成 |

---

## 下一步

论文初稿已完成，可进一步：
- 完善参考文献（补充具体引用信息）
- 生成可视化图表（MSE 对比柱状图、训练曲线等）
- 补充 τ=8/32 的完整结果到正文
- 润色语言和逻辑
