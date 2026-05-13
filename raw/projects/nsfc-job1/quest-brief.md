---
source_path: /data/yantianwei/000_AutoResearch/05_NSFC_JOB1/brief.md
ingested: 2026-05-13
sha256: d444c7bce1d22b26
---
# Quest Brief

## Topic
社交媒体虚假信息主动防御：基于认知免疫学 + 生成式多智能体对抗的范式

## Origin
源自 NSFC 项目申报材料 (notes/260316_innovation_claude_v1.md, notes/260317_technical_roadmap_gemini_detailed_V4.md)，现重定向为**顶会论文冲击**。

## Two Research Points (来自 NSFC notes)

### RC1 —— 多模态解耦的社交高风险诱导感知与认知增强
- **解耦层**：跨模态对比 + 双通道投影 → `F_fact` ⊥ `F_induce`，损失含正交约束 + NER 辅助
  $$L_{decouple} = L_{CL}^{cross} + \lambda_1 \|F_{fact}^\top F_{induce}\|_F^2 + \lambda_2 L_{NER}$$
- **量化层**：冻结 MLLM + 多头适配器 + CoT → 三维认知危害评分 $s_{harm}$（诱导类型 / 模态权重 / 危害强度）
- **疫苗生成**：以机理描述为语义锚，事实骨架 + 可控强度 α 注入诱导噪声，KL 散度 + 危害阈值双约束
- **基座增强**：LoRA PEFT + EWC 防遗忘
  $$L_{EWC} = \sum_j \frac{\Omega_j}{2}(\theta_j - \theta_j^*)^2$$

### RC2 —— 生成式智能体驱动的个体防御策略自适应演化
- **节点建模**：System 1（快速直觉，双向跨模态注意力）× System 2（深度核查，调外部 KG + 危害评分）+ 语义复杂度门控
- **接种响应（Hill 方程）**：
  $$R(\alpha; \text{Node}_i) = \frac{R_{max}^{(i)} \alpha^{n_i}}{K_i^{n_i} + \alpha^{n_i}}$$
- **接种策略**：UCB 探索 - 利用，预算受限下搜索（疫苗类型，剂量，顺序）最优组合
- **记忆与演化**：三级记忆流（短期工作 / 中期情景 / 长期策略）+ PPO 演化检测策略

### （RC3 缺失）
notes 中只覆盖 RC1, RC2；社区级群体免疫屏障部分尚未撰写。

## Goal
冲击 2026 年顶会（候选：NeurIPS / ICLR / ACL / EMNLP / AAAI / WWW 等），从两个研究点中选出一个最有冲击力的核心 idea 进行打磨与实现。

## Communication
中文交互。

## Status
- 初始状态：notes + papers 已存在
- 下一步：scout (聚焦 2025-2026 顶会相关工作)
