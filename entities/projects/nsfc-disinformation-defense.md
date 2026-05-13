---
title: NSFC 项目 — 虚假信息防御
created: 2026-05-13
updated: 2026-05-13
type: entity
tags: [nsfc, disinformation, misinformation-defense, inoculation-theory, social-media]
sources: [raw/projects/nsfc-job1/quest-brief.md, raw/projects/nsfc-job1/quest-status.md, raw/projects/nsfc-job1/technical-roadmap.md]
confidence: medium
---

# NSFC 项目：虚假信息防御

## 概述

国家自然科学基金项目，研究方向为社交媒体虚假信息的智能防御机制。

## 核心概念

### 接种理论 (Inoculation Theory)
- 心理学防御机制：预先暴露微弱版本的虚假信息 → 建立心理免疫
- 类比疫苗：先接触弱化的"病毒"，产生抗体
- 论文：Inoculation theory in the post-truth era, Technique-based inoculation

### 技术路线（从 technical_roadmap.md）
详见 `raw/projects/nsfc-job1/technical-roadmap.md`

### 创新点（从 innovation_ideas.md）
详见 `raw/projects/nsfc-job1/innovation-ideas.md`

## 论文收集（39篇 PDF）

涵盖主题：
- 接种理论与虚假信息防御
- 社交媒体虚假信息传播动力学
- LLM 用于虚假信息检测（ACL 2025, ICLR 2025）
- 多模态虚假信息检测（MLLM-based）
- 预驳斥（Prebunking）设计
- 虚假信息疫苗模型（Finding a vaccine for misinformation）

## 与 IE 研究的交叉

NSFC 项目中的虚假信息检测技术可以与 [[concepts/information-extraction-landscape|信息抽取]] 方法论结合：
- 利用 NER/RE 技术从虚假信息中提取关键实体和关系
- 利用事件抽取检测虚假信息的叙事结构
- KG 可用于建模虚假信息的传播路径

## 关键文件

- `raw/projects/nsfc-job1/quest-brief.md` — 项目简报
- `raw/projects/nsfc-job1/quest-status.md` — 当前状态
- `raw/projects/nsfc-job1/innovation-ideas.md` — 创新点
- `raw/projects/nsfc-job1/technical-roadmap.md` — 技术路线图
- `raw/papers/nsfc-disinfo/` — 39篇相关论文 PDF

See also: [[concepts/information-extraction-landscape]], [[bridgekg-llm]]
