---
source_path: /data/yantianwei/notes/ACL_2025_Document-Level_Relation_Extraction_with_Global_Rel.md
ingested: 2026-05-13
sha256: 8515ab11d7933748
---
# ACL_2025_Document-Level_Relation_Extraction_with_Global_Rel



# 泛读笔记：TPN: Transferable Proto-Learning Network towards Few-shot Document-Level Relation Extraction

## 标题

**TPN: Transferable Proto-Learning Network towards Few-shot Document-Level Relation Extraction**（面向少样本文档级关系抽取的可迁移原型学习网络）

## 作者与单位

| 作者 | 单位 |
|------|------|
| Yu Zhang | 电子科技大学，中国成都 |
| Zhao Kang（通讯作者） | 电子科技大学，中国成都；新疆电子与信息产业研究所，中国新疆 |

**联系方式**：yuzhang2717@gmail.com, zkang@uestc.edu.cn

## 文献一句话总结

本文针对少样本文档级关系抽取中 NOTA（none-of-the-above）关系表示的跨域迁移性差这一难题，提出了一种包含混合编码器、可迁移原型学习器和动态权重校准器的可迁移原型学习网络（TPN），并结合虚拟对抗训练提升模型的跨域性能。

## 代码链接

https://github.com/EchoDreamer/TPN

---

## 背景

### 文档级关系抽取（DLRE）的发展

- 文档级关系抽取因其在从大量数据中提取复杂关系的有效性而日益受到关注
- 传统DLRE方法需要大量标注数据，标注成本高昂

### 少样本学习的引入

- 受元学习（meta-learning）启发，少样本文档级关系抽取（FSDLRE）被提出以解决数据稀缺问题
- 但该任务因 NOTA 关系的建模问题导致性能不佳

### NOTA问题的挑战

- NOTA（None-of-the-above）表示不属于任何预定义关系的样本
- 在FREDo数据集中，NOTA关系占比高达96.4%
- 不同域中NOTA的分布差异显著，导致模型跨域泛化性能严重下降

---

## 动机

### 现有方法的局限性

1. **全局多原型方法的不足**：先前方法直接使用全局多原型建模NOTA原型，在跨域任务中表现有限
2. **域分布差异**：不同域定义的真正关系分布不同，NOTA的分布也差异巨大，导致模型泛化性能显著下降
3. **RAPL方法的缺陷**：
   - 使用两个独立的文本编码器，参数规模大（221MB）
   - 通过硬距离约束生成NOTA原型，缺乏灵活性

### 核心问题

如何设计一个能够自适应不同域的NOTA表示，同时保持较小的模型规模？

---

## 相关工作

### 文档级关系抽取（DLRE）

- **ATLOP**：利用预训练语言模型的注意力机制增强对长文本内容的理解，引入自适应阈值损失处理多分类问题
- **SSAN**：在标准自注意力机制中融入结构依赖

### 少样本句子级关系抽取（FSRE）

- **原型网络**：建立与类无关的度量空间
- **关系描述与知识图谱**：利用外部知识增强关系表示
- **Label Prompt Dropout**：选择性省略标签描述以增强泛化

### 少样本文档级关系抽取（FSDLRE）

- **FREDo数据集**：首个FSDLRE基准数据集，专注于长尾分布和分布外检测挑战
- **MNAV架构**：作为基线分析困难和研究方向
- **RAPL**：提出关系感知原型学习方法来细化关系原型并生成任务特定的NOTA原型，达到当时最优性能

---

## 方法

### 总体框架

TPN包含三个核心组件：
1. **混合编码器（Hybrid Encoder）**
2. **可迁移原型学习器（Transferable Proto-Learner）**
3. **动态权重校准器（Dynamic Weighting Calibrator）**

并辅以**虚拟对抗训练（Virtual Adversarial Training）**增强跨域性能。

### 4.1 混合编码器（Hybrid Encoder）

**目的**：层次化编码输入文档的全局和局部语义信息，结合注意力信息以增强关系表示。

**步骤**：
1. **预训练语言模型编码**：使用PLM获取文档的上下文嵌入
   $$h_{plm} = PLM([w_1, w_2, ..., w_l])$$

2. **实体全局嵌入**：对实体的所有提及进行平均池化得到全局嵌入 $h_{e_i}$

3. **注意力信息提取**：
   - 从PLM获取多头注意力矩阵 $A \in R^{H \times l \times l}$
   - 提取头实体和尾实体的注意力分数，分别记为 $AE_s, AE_o \in R^{H \times l}$
   - 计算实体对的局部化注意力：
   $$A^{(s,o)} = \frac{1}{H}\sum_{i=1}^{H}(AE_s \odot AE_o)$$

4. **局部注意力感知表示**：
   $$c^{(s,o)} = h_{plm}^T \cdot (A^{(s,o)} / 1^T \cdot A^{(s,o)})$$

5. **混合表示融合**：
   $$z_s = tanh(W_s[h_{e_s}; c^{(s,o)}] + b)$$
   其中 $W_s \in R^{d \times 2d}$，$b \in R^{2d}$ 为可学习参数

6. **关系原型计算**：
   $$p_r = \langle sr_1, ..., sr_{\min(|S_r|,\omega)}\rangle$$
   其中 $\omega$ 是选择候选关系实例的最大数量超参数

### 4.2 可迁移原型学习器（Transferable Proto-Learner）

**目的**：学习NOTA关系的正则化表示，消除不同域中的NOTA偏差。

**设计**：
- 使用可学习块学习NOTA关系的正则化表示
- 采用双层MLP设计，作为即插即用的模块

**NOTA原型计算**：
$$p_N = f_\theta(\langle sN_1, ..., sN_\beta \rangle)$$
其中：
- $f_\theta$ 表示可迁移可学习块
- $\beta$ 是从NOTA支持文档中选择实例数量的因子

### 4.3 动态权重校准器（Dynamic Weighting Calibrator）

**目的**：检测关系特定的分类置信度，作为动态权重校准NOTA主导的损失函数。

**分类逻辑**：
- 对于查询文档中的第j个实体对，计算混合嵌入 $q_j$
- 正类：$P^q_j$ 当 $l^r_j > l^N_j$
- 负类：$N^q_j$ 当 $l^r_j \leq l^N_j$
- 其中 $l^r_j = \max(q_j \cdot p_r)$，$l^N_j = \max(q_j \cdot p_N)$

**概率计算**：
$$P(r) = \frac{\exp(l^r_j)}{\exp(l^r_j) + \exp(l^N_j)}$$
$$P(N) = \frac{\exp(l^N_j)}{\sum_{\hat{r} \in N^q_j \cup \{NOTA\}} \exp(l^{\hat{r}}_j)}$$

**损失函数**：
$$L = \sum_{r \in P^q_j} (1 - P(r))^\alpha \log(P(r)) + \log(P(N))$$
其中 $\alpha$ 是超参数

### 4.4 虚拟对抗训练（VAT）

**目的**：平滑语义空间，增强模型鲁棒性，缓解跨域泛化性能挑战。

**方法**：采用FreeLB进行多步PGD对抗训练
- 在词嵌入上添加对抗扰动 $\xi$
- 执行 $\rho$ 次PGD迭代
- 使用硬标签（真实标签）约束对抗样本

**优化目标**：
$$\min_\theta E_{(z_T, y) \sim D}[\frac{1}{\rho}\sum_{t=0}^{\rho-1} \max_{\xi \in S_V} L(\phi_\theta(x + \xi_t), z_T)]$$

**扰动更新**：
$$\xi_t = \Pi_{\|\xi\|_F \leq \epsilon}(\xi_{t-1} + \gamma \cdot g_{adv} / \|g_{adv}\|_F)$$

---

## 实验设计

### 评估指标

- **Macro-F1**：跨关系类型的宏观F1分数
- **Micro-F1**：跨关系类型的微观F1分数

### 基准数据集

1. **FREDo**：从DocRED和sciERC重建，NOTA关系占比96.4%
   - 训练集：62种关系类型（来自Wikipedia）
   - 验证集：16种关系类型（同域）
   - 域内测试：16种关系类型（Wikipedia）
   - 跨域测试：7种关系类型（sciERC）

2. **ReFREDo**：在FREDo上更完整标注的重建版本
   - 训练、验证、域内测试使用Re-DocRED
   - 跨域测试与FREDo相同

### 对比方法

| 方法 | 描述 |
|------|------|
| DL-Base | 仅使用BERT编码文档并平均提及输出 |
| DL-MNAV | 应用MNAV到文档级关系抽取 |
| DL-MNAVSIE | 推理时使用所有单独支持实例 |
| DL-MNAVSIE+SBN | 仅使用NOTA向量 |
| ATLOP | 自适应阈值损失 |
| HCRP | 使用关系描述文件 |
| CHAN | 实例特定注意力 |
| KDDocRE | 最优公开监督DLRE方法 |
| RAPL | 关系感知原型学习 |

### 实现细节

- **编码器**：bert-base-cased
- **优化器**：AdamW with linear warm-up
- **训练轮次**：50k episodes
- **学习率**：2e-6
- **超参数**：$\omega=10, \beta=10, \alpha=1, \gamma=0.15, \epsilon=0.45, \rho=3$
- **梯度裁剪**：max norm 1.0

---

## 结果与分析

### 主要结果（Macro-F1）

| 模型 | FREDo 1-Doc | FREDo 3-Doc | ReFREDo 1-Doc | ReFREDo 3-Doc |
|------|-------------|-------------|---------------|---------------|
| | In/Cross | In/Cross | In/Cross | In/Cross |
| RAPL | 8.75/3.33 | 10.67/5.35 | 15.20/3.51 | 16.35/5.48 |
| **TPN** | **9.12/3.98** | 8.64/4.48 | **15.54/4.72** | **15.73/5.02** |
| TPN(FreeLB) | 8.80/**5.00** | 8.68/**5.93** | - | - |

### Micro-F1结果（ReFREDo）

| 模型 | 1-Doc In | 1-Doc Cross | 3-Doc In | 3-Doc Cross |
|------|----------|-------------|----------|-------------|
| RAPL | 13.91 | 3.87 | 19.99 | 6.34 |
| **TPN** | **22.63** | **7.41** | **26.04** | **7.16** |

### 消融实验结果

| 模型 | 1-Doc In | 1-Doc Cross | 3-Doc In | 3-Doc Cross |
|------|----------|-------------|----------|-------------|
| TPN | 15.54 | 4.72 | 15.73 | 5.02 |
| w/o HE | 2.91 | 2.07 | 2.90 | 2.50 |
| w/o TPL | 4.75 | 2.46 | 4.44 | 2.98 |
| w/o DWC | 12.85 | 4.63 | 13.24 | 5.08 |

### 关键发现

1. **性能优势**：TPN在大多数任务上达到最优或次优性能，尤其在域内任务中表现突出

2. **参数效率**：TPN参数规模约123MB，约为RAPL（221MB）的一半

3. **跨域泛化**：TPN(FreeLB)在跨域设置中取得最佳结果，显著超越其他方法

4. **组件贡献**：
   - 混合编码器（HE）贡献最大，去除后性能下降最显著
   - 可迁移原型学习器（TPL）在域内平均下降11.04%，跨域下降2.15%
   - 动态权重校准器（DWC）作为类平衡预测器发挥作用

5. **Hard vs Single采样**：TPN能够敏感地捕捉从single到hard采样策略的细微变化，获得性能提升

---

## 潜在未来改进方向

### 1. 关系原型表示的优化
- 当前方法在已知类别之间仍难以建立非常清晰的边界
- 可探索更精细的原型对比学习方法

### 3-Doc任务性能提升
- 3-Doc任务有更多支持文档监督信号，但性能提升不如1-Doc显著
- 可能受限于选择实例数量和学习块简单性的限制
- 可设计更高效的支持文档利用机制

### β参数的跨域过拟合问题
- 当β超过20时，跨域性能明显下降，域内略有提升
- 可研究自适应β选择策略或正则化方法

### NOTA偏差的进一步消除
- 尽管TPL模块已显著改善NOTA表示，但仍有提升空间
- 可探索更鲁棒的域适应技术

### 模型轻量化与效率
- 当前使用单一编码器已达到参数减半效果
- 可进一步探索知识蒸馏或模型剪枝技术

### 多模态信息融合
- 当前仅处理文本信息
- 可考虑融入知识图谱、结构化数据等多模态信息增强关系表示