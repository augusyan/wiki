---
source_path: /data/yantianwei/notes/ACL_2025_Spurious_Correlations_and_Beyond.md
ingested: 2026-05-13
sha256: 384f0592698fb1ed
---
# ACL_2025_Spurious_Correlations_and_Beyond



# 文献泛读笔记

## 文章基本信息

- **标题**: Spurious Correlations and Beyond: Understanding and Mitigating Shortcut Learning in SDOH Extraction with Large Language Models
- **作者**: Fardin Ahsan Sakib, Ziwei Zhu, Karen Trister Grace, Meliha Yetişgen, Özlem Uzuner
- **单位**: 
  - Department of Computer Science, George Mason University
  - School of Nursing, George Mason University
  - Department of Biomedical Informatics & Medical Education, University of Washington
  - Department of Information Sciences and Technology, George Mason University
- **发表信息**: arXiv:2506.00134v1 [cs.CL], 2025年5月30日
- **代码链接**: 未提供

## 一句话总结

本文首次系统性地研究了大型语言模型在从临床文本中提取社会健康决定因素（SDOH）时产生的虚假相关性问题，发现酒精/吸烟提及会错误诱导药物使用预测，并揭示了模型性能中存在的性别差异，同时评估了多种提示工程缓解策略的有效性。

---

## 背景

社会健康决定因素（SDOH）包括物质使用、就业和居住条件等，对患者预后和临床决策有重要影响。从非结构化临床文本中提取SDOH信息对下游医疗分析和应用至关重要。近年来，大型语言模型（LLMs）在临床自然语言处理任务中展现出潜力，但可能依赖表面线索进行预测，导致在训练数据分布之外的环境中产生错误预测。

---

## 动机

1. **Shortcut Learning问题**: LLMs可能表现出"捷径学习"行为，利用训练数据中的虚假模式而非学习因果的、可泛化的特征
2. **临床风险**: 错误的药物使用预测可能导致患者污名化、影响临床决策支持系统的信任度和实用性
3. **性别偏见**: 除物质相关触发词外，还存在基于患者性别的系统性性能差异
4. **缺乏系统性研究**: 此前没有针对SDOH提取中虚假相关性的全面分析

---

## 相关工作

1. **SDOH提取**: 从基于规则的方法发展到微调神经网络模型，再到基于提示的方法
2. **Shortcut Learning**: 在NLP标准任务和监督场景中有相关研究，但在零样本/少样本SDOH提取设置中的研究有限
3. **LLM偏见研究**: 已有工作表明微调和基于提示的方法都可能利用虚假相关或表面线索
4. **临床NLP中的偏见**: 已有研究关注模型在临床领域的不当假设问题

---

## 方法

### 3.1 数据集与任务

- **数据集**: 使用SHAC数据集的MIMIC-III部分，包含4405条去标识化的社会历史记录
- **任务**: 药物状态时间分类（current/past/none/unknown）
- **两步骤pipeline**:
  1. 触发词识别：识别与社会历史事件类型对应的文本跨度
  2. 参数解析：对每个触发词应用多选QA提示确定时间状态

### 3.2 实验设置

**模型配置**:
- Zero-Shot: 仅提供任务指令和输入文本
- In-Context Learning (ICL): 提供3个示例演示
- Fine-Tuning (SFT): 在MIMIC子集上微调Llama-3.1-8B

**评估模型**:
- Llama-3.1-70B (zero-shot, ICL)
- Llama-3.1-8B (fine-tuned)
- Qwen-72B (ICL)
- Llama3-Med42-70B (ICL)

### 3.3 缓解策略

1. **Chain-of-Thought (CoT)**: 引导模型通过5步推理过程
2. **Warning-Based**: 包含明确指南对抗虚假相关性
3. **Increased Examples**: 提供更多示例（最多8个）

### 3.4 评估指标

主要使用**假阳性率（FPR）**:
$$FPR = \frac{FP}{FP + TN}$$

其中FP表示假阳性（预测为current/past但实际为none/unknown），TN表示真阴性。

---

## 实验设计

1. **RQ1**: 检验LLMs是否在SDOH提取中表现出虚假相关性
2. **RQ2**: 检验ICL和微调是否减少虚假相关性
3. **RQ3**: 验证表面提及是否因果驱动模型预测（通过移除实验）
4. **RQ4**: 检验是否存在系统性人口统计差异

**实验设置**:
- Original: 在原始笔记上评估
- Without Alcohol/Smoking Triggers: 移除酒精/吸烟提及以测试其因果作用

---

## 数据集

- **SHAC数据集 (MIMIC-III部分)**: 4405条去标识化的社会历史笔记
- **标注内容**: 多种SDOH事件类型（如Alcohol, Drug, Tobacco）及其时间状态
- **人口统计信息**: 通过链接MIMIC-III获取患者人口统计信息
- **示例**: 包含"Patient occasionally uses alcohol. Denies any illicit drug use."等模式

---

## 结果与分析

### 主要发现

#### RQ1: 虚假相关性存在
- Llama-70B零样本设置下：
  - 酒精阳性笔记的FPR: **66.21%**
  - 吸烟阳性笔记的FPR: **61.11%**
  - 酒精/吸烟阴性笔记的FPR显著较低（约28-30%）
- 表明酒精/吸烟提及的存在会虚假诱导药物使用预测

#### RQ2: ICL和微调的效果
- ICL降低酒精阳性FPR: 66.21% → 48.28%
- 微调进一步降低: 32.41%
- 但差距仍然存在，说明预训练中的偏见根深蒂固

#### RQ3: 因果验证
- 移除酒精/吸烟触发词后，FPR持续下降
- 例如：Llama-70B零样本酒精阳性从66.21%降至55.17%
- 证实了这些触发词的因果作用

#### RQ4: 性别差异
- 男性患者假阳性率显著高于女性
- 例如：Llama-70B零样本下，酒精阳性病例男性71.15% vs 女性53.66%
- 物质相关触发词与性别偏见存在复合交互

### 缓解策略效果

| 策略 | 酒精阳性FPR (Llama-70B) |
|------|------------------------|
| Zero-shot | 66.21% |
| ICL | 48.28% |
| CoT | **33.79%** |
| Warning | 40.69% |
| Increased Examples | 45.52% |

**CoT效果最佳**，表明显式推理有助于模型避免表面线索。

---

## 未来改进方向

1. **更鲁棒的去偏技术**: 开发更有效的去偏方法，结合领域知识
2. **对抗性训练**: 引入对抗性示例增强模型鲁棒性
3. **更平衡的数据集**: 策划更均衡的训练数据
4. **模型可解释性**: 使用可解释性技术深入分析触发词影响原因
5. **综合评估框架**: 建立全面的评估框架确保在不同人群中的可靠部署
6. **临床文档实践改进**: 结合模型分析改进临床文档标准和实践

---

## 局限性

1. **数据集局限**: 仅使用SHAC的MIMIC部分，泛化性受限
2. **模型覆盖**: 仅评估开源LLMs，未涵盖闭源模型
3. **因果理解**: 虽通过移除实验验证因果性，但深层原因分析不足
4. **方法范围**: 仅关注生成方法，结果可能不适用于传统pipeline方法
5. **缓解效果**: 缓解策略未能完全解决问题