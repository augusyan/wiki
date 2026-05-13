---
source_path: /data/yantianwei/notes/ACL_2025_Enhancing_NER_by_Harnessing_Multiple_Datasets_with.md
ingested: 2026-05-13
sha256: 32f29e39cd7d990b
---
# ACL_2025_Enhancing_NER_by_Harnessing_Multiple_Datasets_with



# 文献泛读笔记

## 文章标题

**VAE-MS: An Asymmetric Variational Autoencoder for Mutational Signature Extraction**

## 作者信息

- **Ida Egendal**¹'²* (通讯作者)
- **Rasmus Froberg Brøndum**¹'²
- **Dan J Woodcock**³
- **Christopher Yau**⁴
- **Martin Bøgsted**¹'²

## 单位信息

1. Center for Clinical Data Science, Aalborg University and Aalborg University Hospital, Aalborg, 9260, Denmark.
2. Clinical Cancer Research Center, Aalborg University Hospital, Aalborg, 9000, Denmark.
3. Nuffield Department of Surgical Sciences, University of Oxford, Oxford, OX3 9DU, England.
4. Nuffield Department of Women's and Reproductive Health, University of Oxford, Oxford, OX3 9DU, England.

## 发表信息

- arXiv预印本: arXiv:2602.22239v1 [stat.AP] 24 Feb 2026

---

## 一句话总结

本文提出了VAE-MS（用于mutational signature提取的变分自编码器），这是首个将非对称架构与概率建模相结合的变分自编码器方法，在真实癌症基因组数据上实现了比现有最先进方法更优的重建精度。

## 代码链接

GitHub: https://github.com (文中提到软件可在GitHub获取，具体URL未在文本中给出)

---

## 背景

Mutational signature分析是基因组学的一个子领域，旨在识别癌症基因组中的体细胞突变模式，并将其与导致疾病的生物学过程联系起来。Mutational signature分析产生两个矩阵：代表诱变过程特征的signature矩阵，以及指定癌症基因组中可归因于特定过程的突变数量的exposure矩阵。

自2013年Alexandrov等人引入基于非负矩阵分解（NMF）的mutational signature提取方法以来，mutational signature的生物学相关性已通过广泛验证得到确立，证明其与不同生物学过程存在关联。然而，mutational signature分析的临床潜力尚未得到充分利用，尽管该领域已发展超过十年。

---

## 动机

当前mutational signature提取方法存在以下主要局限：

1. **线性假设过于简化**: NMF的严格线性性质无法完全捕捉癌症基因组中mutational过程的复杂性。研究表明，POLE基因校对域的突变与错配修复（MMR）途径相关的两个mutational signature实际上在基因组上存在非线性相互作用。

2. **过度离散（Overdispersion）**: mutational数据中观察到的过度离散表明模型中存在未被解释的方差。严格的确定性方法（如NMF）难以建模这种固有异质性。

3. **非唯一性问题**: NMF存在固有的非唯一性问题，意味着多个等效的分解可以导致相同的重建，影响signature识别的可靠性和一致性。

这些局限导致产生冗余和过于特定的mutational signatures，降低了临床实用性。

---

## 相关工作

1. **SigProfilerExtractor**: 基于NMF的mutational signature提取金标准方法

2. **MUSE-XAE**: 采用非对称设计的深度学习自编码器，使用非线性编码网络捕捉复杂模式，同时通过线性解码网络保持可解释性

3. **SigneR**: 基于贝叶斯NMF的probabilistic方法，使用随机因子矩阵提高对数据可变性的鲁棒性

已有研究表明，probabilistic方法通常优于纯NMF方法。

---

## 方法

### 模型架构

VAE-MS采用非对称架构，包含：

- **编码网络**: 三个全连接层，每层递减维度，每个层后接batch normalization和激活函数。最终编码为exposure矩阵的rate参数 λ ∈ R^(N×K)

- **潜在表示**: 假设潜在分布为泊松分布：
  
  $$W_{n,k} \sim \text{Poisson}(\lambda_{n,k})$$
  
  使用新的泊松重参数化技巧进行采样

- **解码网络**: 单个线性变换无偏置项：
  
  $$\hat{V} = WH$$
  
  其中W是exposure矩阵，H是mutational signature矩阵

### 关键创新点

1. **泊松分布潜在空间**: 选择泊松分布来适应exposure矩阵的非负性，同时保持在数据的原始尺度上

2. **先验分布**: 使用NMF初始化先验泊松rate矩阵λ₀

3. **损失函数**: 使用加权ELBO（证据下界）：
  
  $$\mathcal{L}_{\theta,\phi,\beta} = \sum_{n=1}^{N} \mathbb{E}_{q_\phi(w_n|v_n)}[\log p_\theta(v_n|w_n)] - \beta \cdot D_{KL}(q_\phi(w_n|v_n)||p(w))$$

4. **缩放**: 在前向传播中直接实现缩放，确保H每行之和为1

### 训练策略

- 数据标准化：使用100X标准化处理超突变患者
- 数据划分：64/16/20 训练/验证/测试
- 早停机制：2000个epoch或验证损失50个epoch未改善
- 超参数优化：基于贝叶斯优化的搜索

---

## 实验设计

### 数据集

1. **模拟数据**:
   - S8: 1000个模拟mutational profiles，3个真实signatures（SBS3, SBS5, SBS40）
   - S14: 2700个模拟mutational profiles，21个真实signatures

2. **真实数据**:
   - PCAWG: 2780个来自38种癌症类型的肿瘤-正常全基因组测序数据

所有数据使用SBS96表示（96种单碱基替换类型）

### 比较模型

- SigProfilerExtractor（NMF金标准）
- MUSE-XAE（非对称自编码器）
- SigneR（贝叶斯NMF）

### 性能指标

- **重建精度**: Kullback-Leibler散度(KLD) 和 均方误差(MSE)
- **Signature相似度**: 平均余弦相似度(ACS)
- **稳定性**: 配对平均余弦相似度(PACS) 和 轮廓系数(SS)
- **置信区间**: 95%置信区间包含真实exposure的比例

---

## 结果与分析

### 签名数量选择

- **S8**: NMF方法正确识别3个signatures；自编码器方法在9/10个划分中正确识别
- **S14**: VAE-MS选择13-18个signatures（未识别真实21个）；SigProfilerExtractor和MUSE-XAE选择19-20个
- **PCAWG**: VAE-MS选择16.2±5.68个；SigneR选择25.4±3.10个

### 重建精度

| 数据集 | 指标 | VAE-MS | SigneR | SigProfilerExtractor | MUSE-XAE |
|--------|------|--------|--------|----------------------|----------|
| S8 | Test KLD | 5.9 | **3.1** | 3.2 | 4.2 |
| S8 | Test MSE | 421 | **5** | 12 | 465 |
| S14 | Test KLD | 5.2 | **3.0** | 14.4 | 15.9 |
| S14 | Test MSE | 2347 | **33** | 19831 | 97669 |
| PCAWG | Test KLD | **5.2** | 5.3 | 19.4 | 10.7 |
| PCAWG | Test MSE | **1718** | 53014 | 464576 | 216085 |

**关键发现**:
- 在模拟数据上，NMF方法（SigneR, SigProfilerExtractor）重建精度更高
- 在真实癌症数据（PCAWG）上，VAE-MS在三个指标中取得最低的重建误差
- Probabilistic方法（VAE-MS, SigneR）在PCAWG上显著优于确定性方法

### Signature质量

- 所有模型在S8上达到高PACS（≥0.98）
- S14上SigProfilerExtractor的PACS较低（0.81）
- 确定性模型在模拟数据上显示更高的轮廓系数
- VAE-MS在S14上ACS最低（0.80），表明与真实signature的相似度较低

### 置信区间覆盖

- SigneR在S8上覆盖24%的真实exposure，VAE-MS覆盖7.3%
- S14上VAE-MS覆盖24.6%，SigneR覆盖0.8%
- 两者都未能覆盖一半以上的真实exposure值

---

## 未来可能改进方向

1. **分布选择**: 当前使用泊松分布建模exposure，但mutational count数据已被证明存在过度离散，负二项分布可能更合适

2. **超参数搜索**: 当前仅测试10种超参数配置，建议增加搜索数量以优化性能

3. **方差估计**: 变分分布已知会低估方差，需要改进以更准确地估计不确定性

4. **Signature数量选择**: VAE-MS在模拟数据上难以准确估计真实signature数量，特别是S14，需要改进模型选择标准

5. **非线性交互建模**: 进一步探索signature之间的非线性交互作用

---

## 结论

本文首次提出了用于mutational signature提取的变分自编码器VAE-MS，证明了在真实癌症基因组数据上其重建精度优于三种最先进的方法。VAE-MS的signature在数据划分之间显示出高稳定性，但较低的轮廓系数表明signature分配存在不一致性。该研究展示了深度神经网络与概率建模的结合如何实现更灵活和非线性的mutational signature提取，从而带来更高的重建精度和潜在的临床实用性。