---
source_path: /data/yantianwei/notes/ACL_2025_Summary_Factual_Inconsistency_Detection_Based_on_L.md
ingested: 2026-05-13
sha256: 68270c690f010984
---
# ACL_2025_Summary_Factual_Inconsistency_Detection_Based_on_L



# 文献泛读笔记

## 论文基本信息

- **标题**: SIFiD: Reassess Summary Factual Inconsistency Detection with LLM
- **作者**: Jiuding Yang¹, Hui Liu², Weidong Guo², Zhuwei Rao², Yu Xu², Di Niu¹
- **单位**: 
  - ¹ University of Alberta (阿尔伯塔大学)
  - ² Platform and Content Group, Tencent (腾讯平台与内容集团)
- **发表信息**: arXiv:2403.07557v1, 2024年3月12日

## 一句话总结

本文重新评估了大型语言模型（GPT-3.5和GPT-4）在文档摘要事实一致性检测任务上的表现，并提出了一种名为SIFiD的新型过滤方法，通过句子级别的蕴含评分或语义相似度过滤掉文档中的无关句子，从而提升LLM的检测准确率。

## 代码链接

论文提到代码将开源，但链接显示为"Anonymous"，具体URL未在文中提供。

---

## 背景

文档摘要任务在自然语言生成（NLG）领域变得越来越重要。随着大型语言模型（LLMs）的发展，模型生成自然且事实一致摘要的能力显著提升。然而，快速发展的摘要技术可能导致生成与原文存在事实不一致的摘要，这类不一致非常接近真实事实，传统检测模型难以识别。LLMs本身也面临着检测这些事实不一致的挑战。

---

## 动机

1. **早期LLM应用的局限性**: Luo等人（2023）最早将GPT-3.5用于事实不一致检测，但由于早期GPT-3.5模型能力有限且缺乏有效的检测方法，其表现并未优于传统模型。

2. **新版本LLM的能力提升**: 随着GPT-3.5 Turbo和GPT-4 Turbo的发布，有必要重新评估LLM在此任务上的表现。

3. **提高检测效率**: 原始文档通常包含大量句子，直接将整个文档输入LLM进行评估计算成本高且效率低，需要一种过滤机制来减少输入 token 数量。

---

## 相关工作

### 传统方法
- **问答方法 (QAG)**: Wang等人（2020）、Durmus等人（2020）、Scialom等人（2021）提出通过问答和问答生成来评估事实一致性
- **合成分类器**: Kryściński等人（2020）提出的方法
- **配对方法**: Goodrich等人（2019）、Goyal和Durrett（2020）的方法
- **NLI方法**: Laban等人（2022）证明自然语言推理（NLI）可以有效用于不一致检测

### LLM方法
- Luo等人（2023）首次将GPT-3.5用于摘要事实不一致检测，但效果不佳

---

## 方法

### 3.1 摘要事实不一致检测 with LLMs

使用针对Polytope基准定制的提示模板：

> "Decide if the following summary have any of the specified problems in relation to the corresponding article.
> The problems are categorized as omission, addition, or inaccuracy. Omission means Key point is missing from the summary. Addition means Unnecessary and irrelevant snippets from the Article are included in the summary. Inaccuracy means some information in the summary is not supported by the article.
> Article: {{ Article }}
> Summary: {{ Summary }}
> If the summary has any of the above problems, answer 'No'. Otherwise, answer 'Yes'. Answer (Yes or No):"

### 3.2 SIFiD方法

**核心思想**: 通过句子级别的相关性评分过滤掉与摘要无关的文档句子，只保留最相关的句子进行后续分析。

**步骤**:
1. **计算相关性矩阵R**: 
   $$R = \{Scorer(d_i, s_j)\}_{0 \leq i \leq M, 0 \leq j \leq N}$$
   
   其中 $d_i$ 是文档第i个句子，$s_j$ 是摘要第j个句子

2. **行最大池化**: 提取每个文档句子的最高相关性得分
   $$R_p = \max_{0 \leq j \leq N} r_{i,j}$$

3. **窗口过滤**: 使用阈值β过滤句子，保留上下文连续性
   $$D_{filtered} = \{d_{x-1}, d_x, d_{x+1}\}_{d_x > \beta, 0 \leq x \leq M}$$

4. **LLM评估**: 将过滤后的文档和摘要输入LLM进行一致性判断

### 3.3 Scorer（评分器）

两种评分机制：

1. **蕴含评分器 (Entailment Scorer)**:
   $$score_{ent}^{i,j} = e^0_{i,j} - c_{i,j}$$
   其中 $e^0_{i,j}$ 是初始蕴含分数，$c_{i,j}$ 是矛盾分数

2. **语义相似度评分器 (Semantic Similarity Scorer)**:
   $$score_{sim} = \cos(h_d^i, h_s^j)$$
   使用sentence-transformers库生成句子嵌入，计算余弦相似度

---

## 实验设计

### 数据集
- **SUMMAC** (Laban等人，2022): 包含多个子基准
  - CoGenSum
  - XsumFaith
  - Polytope
  - FactCC
  - SummaEval
  - FRANK

### 模型配置
- **LLM**: gpt-3.5-turbo-1106, gpt-4-1106-preview
- **SIFiD参数**:
  - 蕴含过滤: β = 0.0
  - 语义相似度过滤: β = 0.5
  - 平均句子移除率: 61.3% (蕴含) / 67% (相似度)
- **Sentence-Transformer**: all-mpnet-base-v2

### 对比方法
- 传统方法: DAE, FEQA, QuestEval, SummaC-ZS, SummaC-Conv
- LLM方法: Luo等人（2023）

---

## 结果与分析

### 主要发现

| 方法 | CoGenSum | XsumFaith | Polytope | FactCC | SummaEval | FRANK | 平均 |
|------|----------|-----------|----------|--------|-----------|-------|------|
| SUMMAC-Conv | 64.7 | 66.4 | 62.7 | 89.5 | 81.7 | 81.6 | 74.43 |
| Luo et al. (2023) | 63.3 | 64.7 | 56.9 | 74.7 | 76.5 | 80.9 | 69.5 |
| GPT-3.5 Turbo | 59.9 | 67.6 | 41.0 | 71.3 | 81.4 | 80.2 | 66.9 |
| GPT-4 Turbo | 80.9 | 61.0 | 66.0 | 89.6 | 88.0 | 87.4 | 78.8 |
| **SIFiD-Entailment (GPT-4)** | **82.8** | 58.9 | **74.4** | 89.4 | 87.5 | 86.1 | **79.9** |
| **SIFiD-Similarity (GPT-4)** | **83.1** | 60.2 | 71.0 | **90.6** | 86.8 | 87.7 | **79.9** |

### 关键洞察

1. **GPT-4显著优于GPT-3.5**: GPT-4 Turbo的平均准确率（78.0）远超GPT-3.5 Turbo（69.7），体现了更强的语言理解能力

2. **定制提示模板有效**: 针对Polytope基准调整提示模板后，GPT-4性能从60.9提升到66.0

3. **SIFiD进一步提升GPT-4性能**: SIFiD将GPT-4的平均准确率从78.0提升到79.9

4. **Chain-of-Thought效果不一致**: CoT对GPT-3.5有帮助，但对GPT-4反而导致性能下降

5. **SIFiD对GPT-3.5效果不佳**: 可能因为GPT-3.5处理过滤后不够流畅的文档时能力不足

---

## 未来可能改进方向

1. **探索更高效的过滤策略**: 研究更先进的句子选择和过滤方法，进一步减少输入 token 数量

2. **多模型验证**: 在其他LLM（如Claude、LLaMA）上验证SIFiD方法的有效性

3. **端到端训练**: 不使用预定义的阈值β，而是通过学习方式自动确定过滤条件

4. **降低LLM使用成本**: 鉴于GPT-4等强大LLM的高成本，未来可探索更经济的方案，如蒸馏模型或小型专用模型

5. **多语言支持**: 扩展方法到非英语语言的摘要一致性检测

6. **细粒度检测**: 不仅检测是否一致，还能定位具体的不一致类型（遗漏、添加、不准确）和位置

---

## 局限性

- 使用强大LLM（如GPT-4）的成本较高
- 尽管SIFiD能过滤掉超过60%的句子，输入成本仍然可观
- 任务需要具有足够能力的LLM，只有GPT-4级别及以上的模型才能达到较好效果