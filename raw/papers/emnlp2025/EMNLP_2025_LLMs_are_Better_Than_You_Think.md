---
source_path: /data/yantianwei/notes/EMNLP_2025_LLMs_are_Better_Than_You_Think.md
ingested: 2026-05-13
sha256: 653c4608a2976647
---
# EMNLP_2025_LLMs_are_Better_Than_You_Think



# 文献泛读笔记

## 论文基本信息

- **标题**: BanglaCoNER: Towards Robust Bangla Complex Named Entity Recognition
- **作者**: HAZ Sameen Shahgir§, Ramisa Alam§, Md. Zarif Ul Alam§
- **单位**: Department of CSE, BUET (Bangladesh University of Engineering and Technology)
- **发表信息**: arXiv:2303.09306v2 [cs.CL] 17 Mar 2023
- **代码链接**: https://github.com (文中提到源代码可在GitHub获取)

## 一句话总结

本文针对孟加拉语复杂命名实体识别（CNER）任务，提出了基于条件随机场（CRF）的特征工程方法和基于BanglaBERT的深度学习方法，最终BanglaBERT-large模型在验证集上取得了0.79的F1分数。

---

## 背景

命名实体识别（NER）是自然语言处理中的基础任务，旨在识别和分类文本中的命名实体（如人名、地名、机构名、日期等）。NER对信息抽取、问答、摘要和情感分析等下游应用具有重要价值。

然而，在孟加拉语（Bangla）的复杂命名实体识别方面，目前研究甚少。孟加拉语是全球第七大语言，约有2.5亿使用者，具有复杂的形态和句法结构，给NER系统带来了独特挑战。

---

## 动机

1. **语言代表性不足**: 孟加拉语虽为第七大语言，但在NLP研究中代表性不足
2. **复杂实体识别困难**: 复杂命名实体识别（CNER）比传统NER更具挑战性，需要识别具有内部结构或层次结构的嵌套/重叠实体，以及由多个简单实体组成的复合实体
3. **数据集稀缺**: 缺乏针对孟加拉语CNER的标注数据集
4. **语言特性差异**: 孟加拉语的某些特征（如标题用于识别人名）在英语中有效但在孟加拉语中不适用

---

## 相关工作

1. **CRF在NER中的应用**: CRF是一种广泛使用的概率图模型，用于序列标注任务。Ekbal等人(2008)曾将CRF应用于孟加拉语NER
2. **BERT类模型**: BERT及其变体在多语言NER任务中取得显著成效
3. **ELECTRA架构**: 本文使用的BanglaBERT基于ELECTRA架构，采用生成器-判别器范式，在低计算环境下表现出色
4. **词聚类方法**: Mishra和Diesner(2016)提出的半监督命名实体识别方法

---

## 方法

### 1. 基于特征学习的CRF方法

**特征工程**:
- **词性标注（POS）**: 使用基于CRF的孟加拉语POS标注器，上下文窗口k=2
- **词缀（Suffix/Prefix）**: 孟加拉语地名常以"aloy"、"pur"、"bari"等结尾；女性名字常以"a"结尾
- **k近邻词**: 前后各2个词作为特征
- **词聚类信息**: 使用Word2Vec训练词向量，然后使用k-means聚类，使未知词与同类实体接近
- **Gazetteer列表**: 使用CSE BUET的Bangla T5 NMT模型生成孟加拉语地名和创意词汇的音译列表

### 2. 深度学习方法

**模型选择**: BanglaBERT (large)
- 参数量: 330M
- 预训练语料: Bangla2B+ corpus
- 基于ELECTRA架构，比BERT更高效

**超参数设置**:
- Batch Size: 32
- Max Sequence Length: 64
- Epochs: 6

---

## 实验设计

### 数据集: BanglaCoNER
- **训练集**: 15,300个句子
- **验证集**: 800个句子
- **句子长度**: 2-35词，平均12词
- **标签分布**: 7种NER标签
  - LOC (地点): 3,804
  - GRP (群体): 6,653
  - PROD (产品): 5,152
  - CW (创意词): 5,001
  - CORP (公司): 5,299
  - PER (人物): 6,738
  - O (非实体): 170,000

**数据集特点**: 数据集是合成数据集，存在大量英语词汇（英文原文或音译），可能是翻译产物。

---

## 结果与分析

### CRF特征组合迭代结果

| 特征组合 | F1 Score |
|---------|----------|
| POS Tagger, Suffix | 0.56 |
| POS Tagger, Suffix, k-Neighbor Words | 0.62 |
| POS Tagger, Suffix, k-Neighbor Words, Gazetteer Lists | 0.689 |
| POS Tagger, Prefix, Suffix, k-Neighbor Words | 0.692 |
| POS Tagger, Prefix, Suffix, k-Neighbor Words, k-means clustering | 0.72 |

### BanglaBERT微调结果

| 模型 | Batch Size | Max Seq Length | Epoch | F1 Score |
|------|------------|----------------|-------|----------|
| base | 16 | 128 | 3 | 0.73 |
| large | 16 | 128 | 3 | 0.77 |
| large | 32 | 64 | 3 | 0.76 |
| large | 16 | 128 | 6 | 0.78 |
| large | 32 | 64 | 6 | **0.79** |

### 最终模型各类别性能

| 标签 | Precision | Recall | F1 |
|------|-----------|--------|-----|
| GRP | 0.825 | 0.720 | 0.769 |
| CORP | 0.800 | 0.787 | 0.794 |
| CW | 0.756 | 0.750 | 0.753 |
| PROD | 0.678 | 0.711 | 0.694 |
| LOC | 0.791 | 0.861 | 0.825 |
| PER | 0.908 | 0.958 | 0.932 |
| **Overall** | **0.786** | **0.794** | **0.790** |

### 分析

1. **深度学习 vs 传统方法**: BanglaBERT-large显著优于CRF方法，表明深度学习能更好地捕捉语言模式
2. **类别不平衡问题**: PROD和CW标签的F1分数较低（0.694和0.753），因为这些类别更依赖上下文
3. **特征工程挑战**: 孟加拉语的特征选择比英语更具挑战性，某些英语特征（如标题）无法直接应用
4. **加权损失函数**: 尝试使用加权CrossEntropyLoss反而降低了性能（F1=0.74）
5. **过采样策略**: 对CW和PROD标签进行过采样未带来明显改善

---

## 未来改进方向

1. **改进损失函数**: 尝试不同的类别权重函数以解决数据不平衡问题
2. **外部知识库整合**: 尝试更高效的知识库整合方法（当前因内存限制无法实现）
3. **数据增强**: 探索更多针对孟加拉语的数据增强技术
4. **多语言模型对比**: 进一步比较XLM-R等其他多语言模型
5. **上下文建模**: 加强对创意词(CW)和产品(PROD)等上下文依赖性强的类别的建模能力

---

## 总结

本文首次系统性地研究了孟加拉语复杂命名实体识别任务，通过对比CRF特征工程方法和BanglaBERT深度学习方法，证明了预训练语言模型在低资源语言NER任务中的显著优势。最终模型在验证集上达到0.79的F1分数，为孟加拉语NLP研究做出了重要贡献。