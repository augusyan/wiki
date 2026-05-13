---
source_path: /data/yantianwei/notes/ACL_2025_Information_Extraction_from_Visually_Rich_Document.md
ingested: 2026-05-13
sha256: e866410e95b95392
---
# ACL_2025_Information_Extraction_from_Visually_Rich_Document



# 文献泛读笔记

## 文献标题、作者与发表信息

**标题**：Information Extraction from Visually Rich Documents using LLM-based Organization of Documents into Independent Textual Segments

**作者**：Aniket Bhattacharyya, Anurag Tripathi, Ujjal Das, Archan Karmakar, Amit Pathak, Maneesh Gupta

**单位**：Amazon

**发表信息**：arXiv:2505.13535v1 [cs.IR] 2025年5月18日

---

## 一句话总结

本文提出BLOCKIE方法，通过将视觉丰富的文档（VRD）组织成独立的语义块（semantic blocks），利用大语言模型进行局部推理来完成信息抽取任务，在CORD、FUNSD和SROIE三个公开数据集上实现了新的最先进性能，F1分数提升1-3%。

**代码链接**：无明确代码链接提供

---

## 背景

视觉丰富文档理解（VRDU）是重要的研究课题，广泛应用于发票、表格、合同、收据等结构化或半结构化文档的处理。大规模组织处理的文档数量巨大，尤其是金融或法律文档的处理至关重要。

传统信息抽取流程通常从OCR开始（如Amazon Textract或Tesseract），但仅靠OCR无法解决以下关键挑战：
- 文档格式多样，需要空间推理将文本与语义角色正确关联
- 需要理解上下文关系（如识别'CGST'、'VAT'、'SR'都代表税种）
- 解决方案需能跨异构文档布局和语言泛化

---

## 动机

现有方法存在以下局限性：

1. **传统NLP方法**：基于规则的系统需要已知模板，无法泛化到新格式；深度学习方法需要大量组件级标注

2. **布局感知NLP模型**（如LayoutLM、FormNet、ERNIE-Layout等）：虽然结合文本和几何信息表现良好，但本质上是token分类方法，需要答案显式存在，且难以泛化到新的文档格式

3. **大语言模型**：虽然展示了强大的推理能力，但难以处理与few-shot示例不同的文档，处理复杂布局效率低下

因此，本文提出需要一种能够：
- 跨不同文档格式泛化
- 处理未见过的新文档格式
- 执行值缺失推理（value-absent inference）
- 保持高质量抽取

的方法。

---

## 相关工作

### 传统方法
- 早期依赖基于规则的系统手工特征
- 后来使用RNN、CNN和Transformer提取结构信息
- 局限：需要大量组件级标注，泛化能力差

### 布局感知NLP模型
- LayoutLM系列、FormNet、ERNIE-Layout、GeoLayoutLM等
- 通过跨注意力机制结合文本和边界框嵌入
- 局限：token分类范式限制推理能力，需要答案显式存在

### 大语言模型
- Claude、ChatGPT等商业模型展示零样本推理能力
- LLaVa、CogVLM等开源模型
- 局限：难以处理与示例不同的文档，多实体抽取时prompt难以扩展

---

## 方法

### 核心概念：语义块（Semantic Blocks）

**语义原子（Semantic Atom）**：不可分割的视觉区域，包含形成完整语义单元的文本，同时通过接近度或水平/垂直对齐保持空间连贯性。

**语义块（Semantic Block）**：语义原子的集合，使得集合内每个原子的所有链接（属性-值链接或层级链接）都存在于集合内部。形式化定义为：

$$v(B, B) = v(B, D) = V_E(B)$$

即从B独立提取的值与从完整文档D上下文提取的值相同。

### BLOCKIE三步流程

1. **块创建（Block Creation）**
   - 使用LLM结合文档schema、OCR文本和边界框
   - 通过OCR文本的余弦相似度动态选择few-shot示例
   - 输出：分块后的文本及每块的逐步推理

2. **块解析（Block Parsing）**
   - 找到语义相似的块作为参考
   - 使用schema和few-shot示例指导LLM解析
   - 输出：每个块的局部解析结果

3. **块合并（Combining Blocks）**
   - 将所有块及其解析结果合并
   - LLM作为判断者，综合各块信息填充完整schema
   - 输出：最终的结构化输出

---

## 实验设计

### 数据集
- **CORD**：印尼餐厅收据，1000个样本，30个层级实体
- **FUNSD**：表单理解，199个图像，评估实体链接任务
- **SROIE**：收据信息抽取，626训练/347测试，4个实体

### 基线方法
- 布局感知NLP：LayoutLMv3、FormNet、ERNIE-Layout、GeoLayoutLM等
- LLM方法：DocLLM、LMDX-Gemini Pro、LayoutLLM
- 直接使用Sonnet的Zero-shot和Few-shot

### 评估指标
- 使用微F1分数（micro-F1）
- 5个few-shot示例用于所有实验

---

## 结果与分析

### 性能对比（表1）

| 方法 | FUNSD | CORD | SROIE |
|------|-------|------|-------|
| LayoutLMv3 | 79.37 | 96.98 | 96.12 |
| GeoLayoutLM | 88.06 | 98.11 | 96.62 |
| LMDX-Gemini Pro | - | 95.57 | - |
| Sonnet Few-shot | - | 95.72 | 96.72 |
| **BLOCKIE-Sonnet** | **92.15** | **98.83** | **98.52** |

BLOCKIE在所有三个数据集上均达到最先进性能。

### 小模型也能超越大模型（表2）

- BLOCKIE + Qwen 2.5 32B (96.14% F1) 超越 LMDX-Gemini Pro (95.57%)
- BLOCKIE + Qwen 2.5 7B (87.72% F1) 远超 DocLLM-7B (67.4%)

### 异构性和新格式抗性（表3）

- 训练100个多样化样本：LayoutLMV3从96.78%降至78.79%，BLOCKIE保持94.47%
- 跨数据集泛化（CORD训练→SROIE测试）：LayoutLMV3仅33.43%，BLOCKIE达97.06%

### 块创建的重要性（表4-5）

- 块创建质量与最终性能强相关
- 使用ground truth块，7B模型可达94.38% F1（接近32B的96.14%）

### 值缺失推理能力

在20个需要推断行项目数量的测试样本中，BLOCKIE成功推断18个（90%准确率），展示多步推理能力。

---

## 未来可能改进的方向

1. **图像特征融合**：当前块创建未利用字体大小、粗体/斜体等视觉线索，未来可将这些信息纳入语义块创建

2. **降低延迟**：当前需要顺序调用LLM进行块创建、解析和合并，未来可研究并行化或更高效的架构

3. **更鲁棒的块创建**：当前方法在LLM推理能力较弱时性能受限，未来可探索基于语义块定义的更稳健块创建方法

4. **扩展到更多模态**：将方法扩展到处理更多类型的视觉文档

5. **减少对LLM的依赖**：探索是否可以减少对强推理LLM的依赖，使方法更轻量级

---

## 总结

BLOCKIE通过引入语义块的概念，成功解决了视觉丰富文档信息抽取中的多个挑战：跨格式泛化、新文档格式处理、值缺失推理。其核心创新在于将文档分解为自包含的语义块，使LLM能够进行更聚焦、更可泛化的推理。实验结果表明该方法在三个公开基准数据集上均达到了最先进性能，且对异构文档和小型LLM都具有良好的鲁棒性。