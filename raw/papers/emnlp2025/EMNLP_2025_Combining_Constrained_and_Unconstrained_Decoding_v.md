---
source_path: /data/yantianwei/notes/EMNLP_2025_Combining_Constrained_and_Unconstrained_Decoding_v.md
ingested: 2026-05-13
sha256: 41056752591f6456
---
# EMNLP_2025_Combining_Constrained_and_Unconstrained_Decoding_v



# 泛读笔记

## 论文基本信息

- **标题**: Combining Constrained and Unconstrained Decoding via Boosting: BoostCD and Its Application to Information Extraction
- **作者**: Marija Šakota, Robert West
- **单位**: EPFL, Lausanne, Switzerland
- **发表信息**: arXiv:2506.14901v2 [cs.CL] (2025年9月22日)
- **代码链接**: https://github.com/epfl-dlab/BoostCD

## 一句话总结

本文提出了一种名为Boosted Constrained Decoding (BoostCD)的方法，通过结合约束解码和无约束解码来提升结构化NLP任务的性能，并将该方法应用于封闭信息抽取任务（BoostIE），在分布内和分布外数据上均取得了显著的性能提升。

---

## 背景

信息抽取是许多AI任务的核心，包括知识发现、知识库维护、符号表示和推理等。近年来，越来越多的NLP任务明确要求结构化输出，如代码生成、SQL生成、成分句法分析和信息抽取等。

封闭信息抽取（Closed Information Extraction, cIE）任务要求从非结构化文本中提取事实三元组（主语、关系、宾语），且所有实体和关系必须存在于预定义的知识库（KB）中。这一任务面临的主要挑战包括：

1. **约束动态变化**：现实世界中知识库经常变化，需要在不重训练模型的情况下适应新约束
2. **约束解码的副作用**：当输入数据或约束与训练数据有偏差时，模型可能生成质量较低的输出

---

## 动机

现有的结构化NLP方法通常使用自回归语言模型配合约束解码。约束解码的优势在于：
- 无需在训练时让模型感知约束，可动态适应约束变化
- 当模型已接近正确输出时，可引导模型生成正确结果

然而，约束解码也存在缺点：
- 模型在训练时不知道显式约束，直到推理时才接触到
- 当输入数据或约束与训练数据偏离时，可能生成不太可信的输出

具体问题示例（如图1所示）：
- **无约束模式**：模型生成正确的实体（如"Carol Dollard"），但该实体不在知识库中
- **约束模式**：模型为了适应知识库约束，可能生成错误但相似的实体（如"Carol Douglas"）

作者观察到，约束解码和无约束解码的错误具有互补性，这启发了BoostCD的设计思路。

---

## 相关工作

### 封闭信息抽取（cIE）

1. **传统方法**：结合实体识别、链接和关系抽取，但存在错误传播问题
2. **自回归方法**：
   - GenIE (Josifoski et al., 2022)：基于BART的端到端自回归模型，在REBEL数据集上训练
   - SynthIE (Josifoski et al., 2023)：在合成数据Wiki-cIE Code上训练，改进了对齐和关系分布
3. **检索-阅读架构**：ReLiK (Orlando et al., 2024)，使用检索器和阅读器分离的架构

### 约束解码

- Cao et al. (2021)：通过前缀Trie实现实体消歧约束
- Geng et al. (2024b)：引入语法约束解码
- Park et al. (2024)：语法对齐解码
- Koo et al. (2024)：基于自动机的约束处理

---

## 方法

### BoostCD核心思想

BoostCD灵感来源于经典的boosting集成学习方法，通过迭代组合弱模型来提升性能。其核心思想是：

1. 训练一个新模型（boosted model）来纠正基础模型在phase 1中犯下的错误
2. 该模型基于输入文本、约束解码输出和无约束解码输出的组合来预测真实标签
3. 无需在训练时显式知道约束，保持了对约束变化的灵活性

### 两阶段Pipeline

**Phase 1**：
- 使用基础模型M进行两次解码：
  - 无约束解码：输入文本x，输出$y_u$
  - 约束解码：输入文本x + KB约束，输出$y_c$

**Phase 2**：
- 训练boosted model $M_b$ 将$(x, y_u, y_c)$映射到真实标签$y$
- 在推理时，同样进行两次解码，然后将$(x, \hat{y}_u, \hat{y}_c)$输入boosted model得到最终预测$\hat{y}$

### 在cIE任务上的应用（BoostIE）

**数据准备**：
- 基础模型训练：使用exhaustive数据（文本中的所有事实都可用KB中的实体和关系表达）
- Boosted模型训练：模拟KB动态变化的场景，随机移除部分实体，使模型学会处理"文本中的实体不在KB中"的情况

**模型架构**：
- 基于FlanT5 (Chung et al., 2022)
- 使用线性化表示：$[s]$标记主语，$[r]$标记关系，$[o]$标记宾语，$[e]$标记三元组结束

**DPO微调**：
- 使用Direct Preference Optimization (DPO)进一步提升分布外性能
- 从REBEL数据集中筛选与真实Wikipedia文本相似的样本
- 使用GPT-4对GenIE和SynthIE的输出进行偏好排序

---

## 实验设计

### 知识库
- Wikidata子集：260万实体，888个关系
- 实体使用英文Wikipedia标题表示

### 训练数据
- 基础模型：300K样本（Wiki-cIE Code）
- Boosted模型：额外100K样本（其中40%经过实体移除处理）
- 对比模型SynthIE：使用全部400K样本

### 评估指标
- Micro和Macro精确率、召回率、F1
- 95%置信区间（基于50次bootstrap采样）

### 基线模型
- SynthIE 400k（相同数据训练的合成IE模型）
- ReLiK (relik-cie-large)

---

## 数据集

1. **Wiki-cIE Code** (Josifoski et al., 2023)
   - 合成数据集，约180万训练样本
   - 通过随机游走在Wikidata子图上生成三元组，再由LLM生成对应文本
   - 10K验证集，50K测试集

2. **Wikipedia文本**
   - 用于分布外评估
   - 随机选取Wikipedia文章，每篇提取最多4句话的段落

3. **REBEL数据集**
   - 用于DPO微调
   - 通过远程监督构建的事实三元组数据集

---

## 结果与分析

### Wiki-cIE Code评估（分布内）

| 模型 | Micro F1 | Macro F1 |
|------|----------|----------|
| BoostIE (constrained) | **52.35** | **48.81** |
| BoostIE + DPO (constrained) | 52.28 | 48.29 |
| SynthIE 400k (constrained) | 35.30 | 36.25 |
| ReLiK (filtered) | 21.79 | 12.92 |

**关键发现**：
- BoostIE在Micro F1上比SynthIE提升17.05分，Macro F1提升12.56分
- 当KB中实体被移除时，BoostIE性能下降幅度小于SynthIE
- DPO在分布内数据上未带来显著提升

### Wikipedia文本评估（分布外）

| 模型 | Micro F1 | Macro F1 |
|------|----------|----------|
| BoostIE + DPO | **34.93** | **20.87** |
| BoostIE | 19.33 | 13.49 |
| ReLiK | 23.99 | 8.33 |
| SynthIE 400k | 8.76 | 5.35 |

**关键发现**：
- BoostIE + DPO在分布外数据上显著优于其他方法
- Micro F1提升10.94分，Macro F1提升12.54分
- DPO对分布外性能提升至关重要

### 错误分析

在50个Wikipedia样本上的错误类型分析：

| 错误类型 | SynthIE | ReLiK | BoostIE | BoostIE + DPO |
|----------|---------|-------|---------|---------------|
| 不完整三元组 | 0.33 | 0.38 | 0.32 | 0.38 |
| 错误相关三元组 | 0.36 | 0.28 | 0.26 | **0.12** |
| 实体误分类 | 0.09 | 0.04 | **0.00** | **0.00** |
| 无关三元组 | 0.60 | 0.14 | 0.28 | **0.08** |
| 实体中心化 | 0.16 | **0.00** | 0.11 | 0.06 |

**关键发现**：
- BoostIE显著降低了实体误分类和无关三元组错误
- 这些错误正是约束解码的典型问题
- DPO进一步减少了错误相关三元组和无关三元组

### 按关系频率分析

BoostIE在所有关系频率桶上均优于SynthIE，且在稀有关系上保持稳定性能。ReLiK在稀有关系上表现较差。

---

## 未来可能改进方向

1. **实体表面形式变化处理**
   - 当前pipeline可能难以处理文本中以缩写或别名形式出现的实体
   - 可考虑结合外部知识或LLM来处理这类情况

2. **推理效率优化**
   - 当前需要三次模型运行（两次基础模型 + 一次boosted模型）
   - 可探索并行化或模型蒸馏来提升效率

3. **训练数据改进**
   - 当前合成数据与真实文本存在分布差异
   - 可通过改进合成数据生成策略，引入更多实体多样性

4. **多步Boosting**
   - 当前使用单步boosting，可探索多步迭代进一步提升性能

5. **扩展到其他结构化任务**
   - BoostCD可应用于代码生成、JSON生成、句法分析等任务
   - 值得在未来工作中验证

6. **处理真实世界文本**
   - Wikipedia仍属于较为规范的文本
   - 需在更真实的文本上进行评估，发现更多失败模式