---
source_path: /data/yantianwei/notes/ACL_2025_Graph-Structured_Trajectory_Extraction_from_Travel.md
ingested: 2026-05-13
sha256: f4de3787141ba43c
---
# ACL_2025_Graph-Structured_Trajectory_Extraction_from_Travel



# 泛读笔记：Graph-Structured Trajectory Extraction from Travelogues

## 文献信息

**标题**：Graph-Structured Trajectory Extraction from Travelogues（从游记中提取图结构化轨迹）

**作者**：Aitaro Yamamoto♣,∗, Hiroyuki Otomo♡, Hiroki Ouchi♣,♡,♦,†, Shohei Higashiyama♠,♣, Hiroki Teranishi♦,♣, Hiroyuki Shindo♢, Taro Watanabe♣

**单位**：
- ♣ NAIST（奈良先端科学技术大学院大学）
- ♠ NICT（信息通信研究机构）
- ♦ RIKEN（理化学研究所）
- ♡ CyberAgent, Inc.
- ♢ MatBrain, Inc.

**发表信息**：arXiv:2410.16633v1，2024年10月22日

---

## 一句话总结

本文提出了一种基于图的轨迹表示方法（访问顺序图），能够同时保留地理位置的层级关系和访问的时间顺序，并构建了首个用于图结构化轨迹提取的基准数据集ATD-VSO。

**代码与数据集链接**：https://github.com/naist-nlp/atd-mcl（作者提到将发布代码和数据集，链接待补充）

---

## 背景

游记（Travelogues）是分析人类旅行行为的重要数据来源，广泛应用于旅游信息学和人文学研究。然而，从文本中自动提取人类移动轨迹的研究存在两个问题：

1. **轨迹表示不当**：现有研究将轨迹视为位置序列，但这种方法无法准确表示地理位置包含关系（如"京都市"包含"京都站"）
2. **缺乏基准数据集**：现有研究使用私有数据集，缺乏公开的标注数据

---

## 动机

传统的序列式轨迹表示存在局限性：
- 当一个地理位置在地理上包含另一个位置时（如城市与车站），两者无法在单一序列中正确排列
- 序列无法表达地理层级关系

因此，需要一种新的图结构化表示方法来同时捕获：
- 访问的时间顺序（Transition Relation）
- 地理位置的包含关系（Inclusion Relation）

---

## 相关工作

### 访问状态预测相关
- **谓词中心分析**：SPACEBANK系列研究关注动词表达的运动（Pustejovsky et al., 2012-2015）
- **位置中心分析**：部分研究从社交媒体和临床文档中提取位置访问状态（Li & Sun, 2014; Matsuda et al., 2018）

### 访问顺序预测相关
- Ishino et al. (2012)：从灾难相关推文中提取交通信息
- Wagner et al. (2023)：从证词视频转录中提取轨迹
- Kori et al. (2006)：从博客中提取游客典型路线

**关键区别**：本文采用图而非序列作为轨迹表示，能够保留地理层级和时间顺序。

---

## 方法

### 1. 访问状态预测（Visit Status Prediction, VSP）

**任务定义**：
- **实体级VSP**：给定文档中的实体集合E，为每个实体$e_q \in E$分配访问状态标签$y \in L_e$
- **提及级VSP**：给定实体$e_q = \{m^{(q)}_1, ..., m^{(q)}_{|e_q|}\}$, 为每个提及$m^{(q)}_i$分配访问状态标签$y \in L_m$

**标签定义**：
- 实体标签：Visit（访问）、Other（其他）
- 提及标签：Visit、PlanToVisit、See、Visit-Past、Visit-Future、UnkOrNotVisit

**基线系统**：
采用两阶段方法：
1. 使用LUKE模型预测每个提及的标签
2. 通过提及标签聚合（MLA）规则确定实体标签：
   - 如果实体至少有一个提及被标记为Visit或PlanToVisit，则实体标签为Visit
   - 否则为Other

### 2. 访问顺序预测（Visiting Order Prediction, VOP）

**访问顺序图定义**：
- **节点**：访问状态为Visit的实体
- **边**：
  - 包含关系（Inclusion）：表示地理上的包含关系（如"Nara City"包含"Todaiji Temple"）
  - 过渡关系（Transition）：表示访问的时间顺序，同一父节点下的实体之间

**子任务**：
1. **包含关系预测（IRP）**：为每个实体预测父实体
2. **过渡关系预测（TRP）**：为每个实体预测后续访问的实体

**基线系统**：
- 使用LukeForEntityPairClassification模型
- 采用序列排序解码（Sequence Sorting Decoding）方法

---

## 实验设计

### 数据集划分
- 训练集：70篇文档
- 开发集：10篇文档
- 测试集：20篇文档

### 评估设置
- VSP：准确率和宏平均F1分数
- IRP：包含实体对的F1分数
- TRP：过渡实体对的F1分数

### 模型训练
- 使用预训练的 multilingual LUKE（VSP）和 Japanese LUKE（VOP）
- 训练10个epoch，batch size为16/8，学习率5e-6

---

## 数据集

**ATD-VSO（Arukikata Travelogue Dataset with Visit Status and Visiting Order Annotation）**：

| 统计项 | 训练集 | 开发集 | 测试集 | 总计 |
|--------|--------|--------|--------|------|
| 文档数 | 70 | 10 | 20 | 100 |
| 句子数 | 4,254 | 601 | 1,469 | 6,324 |
| 提及数 | 3,782 | 505 | 1,102 | 5,389 |
| 实体数 | 2,339 | 316 | 699 | 3,354 |
| 包含+过渡关系 | 2,343 | 329 | 697 | 3,369 |

---

## 结果与分析

### 1. 访问状态预测（VSP）

| 方法 | 准确率 | 宏平均F1 |
|------|--------|----------|
| Majority Label（提及级） | 0.679 | 0.135 |
| LUKE（提及级） | 0.789 | 0.468 |
| Majority Label（实体级） | 0.823 | 0.451 |
| LUKE + MLA（实体级） | 0.862 | 0.740 |

**分析**：
- Visit标签表现优异（F1: 0.872-0.918）
- UnkOrNotVisit/Other标签表现有限（F1: 0.482-0.561）
- 主要错误是将UnkOrNotVisit误分类为Visit

### 2. 包含关系预测（IRP）

| 方法 | F1分数 |
|------|--------|
| Random | 0.043 |
| Flat | 0.244 |
| LUKE | 0.355 |

**分析**：
- 整体性能较低（F1=0.355）
- 对于深度≥2的实体，LUKE表现较好（F1=0.425）
- 对于父实体为ROOT的实体，性能很差

### 3. 过渡关系预测（TRP）

| 方法 | All | Fwd. | Rev. |
|------|-----|------|------|
| Random | 0.190 | 0.247 | 0.061 |
| OccOrder (early mention) | 0.730 | 0.773 | 0 |
| OccOrder (visit status) | 0.758 | 0.794 | 0.386 |
| LUKE (naïve score) | 0.680 | 0.737 | 0.298 |
| LUKE (sequence sorting) | 0.748 | 0.796 | 0.366 |

**分析**：
- 序列排序解码有效提升了性能（约0.07 F1）
- 逆序实体对预测困难
- 文本中描述顺序与访问顺序高度相关

---

## 未来改进方向

1. **访问状态预测改进**：
   - 构建端到端模型，整合实体的所有提及信息和更广泛的文档上下文
   - 改进对事实性陈述与访问描述的区分能力

2. **包含关系预测改进**：
   - 预训练时注入地理信息（如GeoLM）
   - 使用地理编码特征（预测坐标、形状等）
   - 改进ROOT预测机制

3. **过渡关系预测改进**：
   - 扩展实体上下文，整合所有提及信息
   - 利用间接过渡关系作为辅助任务

4. **端到端系统**：
   - 开发轨迹提取与地理定位的集成系统
   - 将提取的轨迹与地图上的点/区域关联

---

## 总结

本文提出了访问顺序图这一创新性的轨迹表示方法，并构建了首个用于图结构化轨迹提取的基准数据集。实验表明，系统在访问状态预测和过渡关系预测上表现良好，但在包含关系预测上仍面临挑战，这提示未来需要将地理层级结构知识更好地融入模型中。