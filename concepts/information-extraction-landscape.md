---
title: 信息抽取技术全景
created: 2026-05-13
updated: 2026-05-13
type: concept
tags: [information-extraction, ner, relation-extraction, event-extraction, llm]
sources: [raw/papers/acl2025/, raw/papers/emnlp2025/]
confidence: medium
---

# 信息抽取技术全景

## 子任务

### 1. 命名实体识别 (NER)
识别文本中的实体（人名、地名、组织名、专有术语等）。

**2025 前沿趋势：**
- 从序列标注 → 生成式范式（LLM 直接生成实体列表）
- Zero-shot/Few-shot 泛化能力提升
- 多模态 NER（视觉丰富文档、语音 ASR 纠错）
- 评估方法需重新审视（随机划分导致虚高指标）

### 2. 关系抽取 (RE)
识别实体之间的语义关系。

**2025 前沿趋势：**
- 文档级关系抽取（跨句子推理）
- 开放关系抽取（不预定义关系类型）
- 元学习 + 少样本关系学习
- 跨语言关系迁移

### 3. 事件抽取 (EE)
从文本中识别事件触发词和论元。

**2025 前沿趋势：**
- 事件模式图（图结构建模事件关系）
- LLM 标注替代人工标注（降低成本）
- 临床/医学领域事件抽取
- 时序事件推理

### 4. 知识图谱构建
从非结构化文本自动构建知识图谱。

**关键挑战：**
- 本体驱动 vs 开放抽取
- KG + LLM 协同（检索增强生成）
- 多模态 KG 构建

## 与个人研究的关联

- **BridgeKG-LLM**：知识图谱增强时序预测 → 可以借鉴 KG 构建方法论
- **NSFC 项目**：虚假信息防御 → 可以利用 IE 技术做信息可信度评估
- **未来方向**：将 IE 技术应用于桥梁工程文档的自动化知识抽取

See also: [[concepts/paper-collection-acl-emnlp-2025]], [[bridgekg-llm]]
