---
source_path: /data/yantianwei/notes/ACL_2025_Towards_Robust_Universal_Information_Extraction.md
ingested: 2026-05-13
sha256: 73d35ddf520661f8
---
# ACL_2025_Towards_Robust_Universal_Information_Extraction



# 泛读笔记：Towards Robust Universal Information Extraction: Dataset, Evaluation, and Solution

## 文献基本信息

**标题**: Towards Robust Universal Information Extraction: Dataset, Evaluation, and Solution

**作者**: Jizhao Zhu¹'², Akang Shi², Zixuan Li¹*, Long Bai¹, Xiaolong Jin¹*, Jiafeng Guo¹, Xueqi Cheng¹

**单位**: 
1. Key Laboratory of Network Data Science and Technology, Institute of Computing Technology, Chinese Academy of Sciences
2. School of Computer Science, Shenyang Aerospace University, Shenyang, China

**发表信息**: arXiv:2503.03201v1 [cs.CL] 2025年3月5日

**代码链接**: https://huggingface.co/golaxy/KnowCoder-7B-base

---

## 一句话总结

本文针对通用信息抽取（UIE）模型的鲁棒性问题，构建了一个包含14种对抗扰动的大规模基准数据集RUIE-Bench，并提出了一种基于损失引导的数据增强方法（LDA），仅使用15%的增强数据即可达到与全数据训练相当的性能。

---

## 背景

信息抽取（IE）旨在从非结构化文本中根据预定义的实体、关系和事件类型提取结构化知识。通用信息抽取（UIE）通过单一模型统一抽取各种知识类型，近年来取得了显著进展。然而，现有研究主要关注UIE模型的总体性能提升，通常在固定测试集上评估，往往忽略了模型的鲁棒性（泛化能力），而这对于处理真实世界文本至关重要。

---

## 动机

现有鲁棒性基准数据集存在两个关键局限：
1. **扰动类型单一**：仅针对单一信息抽取任务生成有限范围的扰动，难以有效评估UIE模型在各种IE任务上的鲁棒性
2. **扰动生成方法粗糙**：依赖小模型或手工规则生成对抗样本，往往产生不自然的对抗样本

鉴于大型语言模型（LLM）强大的生成能力，本文利用LLM生成更多样化、更真实的扰动，以构建更全面的鲁棒性评估基准。

---

## 相关工作

### 通用信息抽取（UIE）
- **基于分类的方法**：采用端到端联合抽取模式，通过全局依赖建模增强跨任务交互
- **基于生成的方法**：生成结构化信息而非从纯文本中抽取结构化信息
- **基于LLM的UIE方法**：如InstructUIE使用文本风格提示，KnowCoder使用代码风格提示将UIE转化为代码生成任务

### IE鲁棒性研究
- **NER鲁棒性**：RockNER使用基于规则的方法和BERT生成扰动；Jin等人使用解耦和词归因方法；Srinivasan和Vajjala应用多种扰动
- **RE鲁棒性**：Li等人通过随机替换词生成对抗样本；Nolano等人通过替换三元组中的实体生成对抗样本
- **ED鲁棒性**：Liu等人使用GloVe替换相似词生成对抗样本

### 数据增强提升鲁棒性
现有方法通常需要大量对抗样本，但均未关注使用少量增强样本提升模型鲁棒性。

---

## 方法

### 1. RUIE-Bench数据集构建

利用LLM（GPT-4）模拟不同类型的扰动，包括：

| 扰动类型 | 描述 |
|---------|------|
| **Replace Entity/Triple/Trigger** | 替换实体/关系三元组/事件触发词，保持类型不变 |
| **Change Context** | 替换上下文词汇，使用[MASK] token让LLM生成替换词 |
| **Extend Sentence** | 添加语义相关内容增加句子复杂度 |
| **Typo Injection** | 基于规则注入拼写错误（针对长度>6词的词） |
| **Lowercase Conversion** | 转换为小写（除首词首字母外） |

### 2. Loss-guided Data Augmentation (LDA)

**核心思想**：关注模型当前表现不佳的样本（损失值较高），以加速收敛并提升整体性能。

**算法流程**：
1. 使用LLM基于原始训练集生成增强数据 $D_{aug}$
2. 在原始数据 $D$ 上微调初始模型 $M$ 得到 $M_0$
3. 迭代 $t$ 次：
   - 使用 $M_{t-1}$ 计算每个增强样本的推理损失 $L_i$
   - 按损失降序排序，选择前 $\beta$ 比例的样本形成 $D^{(t)}_{retrain}$
   - 使用 $D^{(t)}_{retrain}$ 微调 $M_{t-1}$ 得到 $M_t$
   - $\beta \leftarrow \beta/2$
4. 当验证集性能提升小于阈值 $\delta$ 时停止

---

## 实验设计

### 数据集
- **NER**: ACE05-Ent, CoNLL03, WikiANN
- **RE**: ACE05-Rel, NYT
- **ED**: ACE05-Evt

从各测试集进行分层采样，共11580个样本。

### 评估指标
基于span的offset Micro F1：
- NER：实体边界和类型均正确
- RE：关系类型、头实体、尾实体均匹配
- ED：事件类型和触发词均正确

### 基线模型
- **开源LLM**: Qwen2.5-3B/7B/14B-Instruct, Llama3-8B-Instruct, GLM-4-9B-Chat, CodeLlama-7B-Instruct, InternLM2.5-7B-Chat, Vicuna-7B-v1.5
- **闭源LLM**: GPT-3.5-turbo, GPT-4-turbo, DeepSeek-V3, GLM4-Plus
- **传统IE模型**: Stanza, TNER (NER), PFN (RE), EEQA (ED)
- **UIE模型**: UIE, InstructUIE-11B, YAYI-UIE-13B, KnowCoder-7B

### 训练配置
- 初始模型：KnowCoder-7B-base
- LoRA rank=32, alpha=64
- 学习率：$3 \times 10^{-4}$，warmup=0.1，dropout=0.1
- 序列长度：2048，batch size：256
- LDA选择比例 $\beta=10\%$，收敛阈值 $\delta=0.3$

---

## 结果与分析

### RUIE-Bench主要发现

1. **数据增强模型表现最佳**：KnowCoder-7B-RobustLDA仅使用15%增强数据达到与全数据训练相当的性能

2. **LLM相对更鲁棒**：LLM-based模型相比传统IE模型在某些扰动下表现出更好的鲁棒性

3. **所有LLM性能显著下降**：在各种扰动下，尤其是NER和RE任务，LLM面临严重的鲁棒性问题

4. **模型规模与鲁棒性正相关**：Qwen模型在不同参数规模下，参数增加通常伴随鲁棒性提升（ED任务除外）

### 未见数据集结果

| 模型 | NER | RE | ED | 平均 |
|------|-----|-----|-----|------|
| KnowCoder-7B-RobustLDA | 67.2 | 54.6 | 60.1 | **60.6** |
| KnowCoder-7B-Robust | 62.8 | 47.4 | 56.6 | 55.6 |

LDA在未见数据集上平均提升8.9% F1，表明其具有更好的泛化能力。

### 详细分析

- **Replace Entity/Triple/Trigger扰动**导致模型性能显著下降，说明原始模型可能记忆了特定模式而非基于上下文推理
- **Lowercase Conversion**扰动对某些数据集影响显著，因为这些数据集标注中包含较多大写字符
- **KnowCoder-7B-RobustLDA**在几乎所有扰动下都有显著改进，验证了LDA策略的有效性

---

## 未来可能改进方向

1. **更真实的扰动生成**：当前扰动仍无法覆盖真实世界中的多样噪声，需探索更真实的扰动生成方法

2. **更多LLM评估**：由于成本和资源限制，未对更多LLM进行鲁棒性评估

3. **增强数据质量约束**：损失引导数据增强方法的性能提升可能受限于增强数据的质量

4. **更多扰动类型**：可探索更多对抗扰动类型，如语义相似但结构不同的句子、跨语言扰动等

5. **计算效率优化**：进一步优化LDA策略的计算效率，降低训练成本