---
source_path: /data/yantianwei/000_AutoResearch/05_NSFC_JOB1/status.md
ingested: 2026-05-13
sha256: ac9c293d4b31cc55
---
# Quest Status

| Field | Value |
|---|---|
| Current stage | experiment (pilot completed, waiting for data) |
| Last gate decision | Gate 1: scout → idea (隐式通过，用户选定方案 A INOC-VAX) |
| Pending action | 等待 Fakeddit 数据上传，启动全量实验 |
| Selected idea | INOC-VAX (机理锚疫苗 + Hill+UCB 接种) |
| Language | 中文 |
| Goal | 顶会论文冲击（基于 NSFC RC1/RC2 之一） |

## Stage log

- 2026-05-11: quest 初始化。读取 NSFC notes 与 papers/，未发现已有 baseline / experiment 状态。用户希望 "从 idea 开始与我交互打磨"，但需要先用 scout 补齐 2025-2026 顶会相关工作。
- 2026-05-11 (17:00-19:00): **experiment 阶段启动**。完成以下小批量验证：
  - 硅基流动 API 客户端验证通过（Qwen3-8B 可用）
  - 疫苗生成模块验证通过（机理锚 prompt + α 强度控制）
  - vLLM 本地服务验证通过（Qwen2-VL-2B-Instruct @ CUDA 12.8）
  - Agent 检测器验证通过（JSON 输出解析，label/confidence 提取）
  - Hill 曲线拟合验证通过（scipy curve_fit）
  - UCB1 / LinUCB / Thompson Sampling 策略验证通过
  - **端到端 quick test v2 通过**：疫苗生成 → 接种 → 检测 → Hill 拟合 → Bandit 策略全链路打通
  - 资源缺口识别：数据集缺失、本地 VL 模型尺寸单一、API 模型成本需评估
- 2026-05-11 (20:41-22:24): **Qwen3-VL-4B-Instruct 本地部署**。用户完成权重下载，vLLM 服务在 port 8001 启动成功，验证通过。
- 2026-05-11 (22:24) → 2026-05-12 (10:26): **Pilot 50 样本验证完成**。关键结果：
  - 400 条疫苗全部生成成功（Qwen3.5-397B-A17B）
  - Agent 检测诱导效果显著：local_4B 58.8%，api_8B 57.5% 被诱导为假新闻
  - 机制有效性排序：Conspiracy > Discrediting > Impersonation > Emotional
  - **关键发现**：Hill 拟合因 α 档数不足（仅 2 档）退化为常数，全量实验需 ≥3 档 α
  - UCB1 / LinUCB 策略模拟正常完成
- 2026-05-12 (10:26-): **等待 Fakeddit 数据上传**。全量实验脚本、可视化模块、论文实验矩阵已全部就绪。
