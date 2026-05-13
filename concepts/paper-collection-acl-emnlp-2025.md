---
title: ACL/EMNLP 2025 论文笔记收集 — 信息抽取专题
created: 2026-05-13
updated: 2026-05-13
type: concept
tags: [literature-survey, information-extraction, ner, relation-extraction, event-extraction, acl2025, emnlp2025]
sources: [raw/papers/acl2025/, raw/papers/emnlp2025/]
confidence: high
---

# ACL/EMNLP 2025 论文笔记收集

## 概述

该集合包含 **63 篇** 论文笔记，覆盖 ACL 2025（45篇）和 EMNLP 2025（18篇），聚焦于 **信息抽取（Information Extraction）** 及相关领域。原始笔记位于 `/data/yantianwei/notes/`。

## 主题分布

### 命名实体识别 (NER)
- Zero-shot / Few-shot NER 方法
- 跨领域/跨语言 NER 泛化
- 多数据集联合训练（Enhancing NER by Harnessing Multiple Datasets）
- 生成式标注方法（Generative Annotation）
- 评估方法反思（Random Splitting Negatively Impacts NER Evaluation）

### 关系抽取 (RE)
- 文档级关系抽取（Document-Level RE with Global Rel）
- 开放关系抽取（OpenNER）
- 少样本关系学习（Meta-Semantics Augmented Few-Shot Relational Learning）
- 跨语言信息抽取（Translation and Fusion Improves Cross-lingual IE）

### 事件抽取 (EE)
- 事件论元数据增强（Document-Level Event-Argument Data Augmentation）
- LLM 标注用于临床事件抽取
- 事件模式图（Event Pattern-Instance Graph）
- 时序关系抽取（Temporal Relation Extraction in Clinical Texts）

### 知识增强
- 异质知识融合（Incorporating Heterogeneous Knowledge）
- 多模态检索增强（Enhancing Multimodal Retrieval）
- 知识图谱与 LLM 结合

### 评估与鲁棒性
- LLM 偏好对事件论元抽取的影响
- 虚假相关与鲁棒性
- 自动评估改进

## 使用方法

需要查阅特定论文时，搜索对应笔记：
```bash
search_files "关键词" path="/data/yantianwei/wiki/raw/papers/acl2025/"
search_files "关键词" path="/data/yantianwei/wiki/raw/papers/emnlp2025/"
```

或告诉我 "从论文笔记中找关于 XX 的内容"，我会交叉检索。

See also: [[concepts/information-extraction-landscape]], [[entities/projects/nsfc-disinformation-defense]]
