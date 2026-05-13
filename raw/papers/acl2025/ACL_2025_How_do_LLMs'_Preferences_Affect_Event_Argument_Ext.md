---
source_path: /data/yantianwei/notes/ACL_2025_How_do_LLMs'_Preferences_Affect_Event_Argument_Ext.md
ingested: 2026-05-13
sha256: f07aff6c53803bf5
---
# ACL_2025_How_do_LLMs'_Preferences_Affect_Event_Argument_Ext



# 文献泛读笔记

## 文章基本信息

- **标题**: Online Iterative Reinforcement Learning from Human Feedback with General Preference Model
- **作者**: Chenlu Ye*, Wei Xiong*, Yuheng Zhang*, Hanze Dong*, Nan Jiang, Tong Zhang
- **单位**: University of Illinois Urbana-Champaign, Salesforce AI Research
- **发表信息**: NeurIPS 2024 (38th Conference on Neural Information Processing Systems)
- **arXiv编号**: arXiv:2402.07314v3 [cs.LG] (2024年11月12日)

## 一句话总结

本文研究在通用偏好预言机（General Preference Oracle）下的强化学习从人类反馈（RLHF）问题，提出了基于反向KL正则化的双人零和博弈框架，并设计了离线学习和在线迭代学习的样本高效算法，理论证明了算法的有限样本收敛界。

## 代码链接

文中未提供明确的代码仓库链接，但详细描述了实际实现方法，包括使用self-play IPO近似Nash均衡预言机，以及使用拒绝采样作为探索增强器。

---

## 背景

### RLHF的发展现状

强化学习从人类反馈（RLHF）已成为对齐大型语言模型（LLM）与人类价值观和偏好的关键技术，成功应用于ChatGPT、Claude、Bard等系统。标准RLHF流程包括：
1. 预训练模型初始化
2. 监督微调（SFT）
3. 奖励建模
4. 策略优化（通常使用PPO）

### 传统方法的局限性

现有方法通常假设存在底层奖励函数，并且人类偏好服从Bradley-Terry (BT) 模型：
$$P(a_1 \succ a_2|x, a_1, a_2) = \sigma(R^*(x, a_1) - R^*(x, a_2))$$

然而，BT模型存在以下问题：
- **传递性假设**：假设人类偏好具有传递性（即A>B且B>C则A>C），但人类决策中已观察到非传递性
- **准确率有限**：实践中学习的BT模型准确率仅约70%
- **无法捕捉复杂偏好**：无法表达非传递性偏好和群体级偏好差异

### 通用偏好预言机

本文采用更一般的偏好预言机定义（定义1）：
$$y \sim Ber(P(a_1 \succ a_2|x, a_1, a_2))$$

其中$P: X \times A \times A \rightarrow [0,1]$是任意偏好函数，无需满足BT模型的假设。

---

## 动机

1. **更一般化的偏好建模**：通用偏好预言机可以捕捉非传递性偏好，支持AI评估者（如GPT-4）作为反馈源
2. **排序准确率优势**：实验表明，偏好模型在推理任务上比奖励模型准确率更高（表1）
3. **理论与实践差距**：现有RLHF理论研究主要基于BT模型假设，与实际应用存在差距

### 实证证据

论文中的实验显示：
- 使用Gemma-2B-it作为基础模型，偏好模型在推理任务上的准确率（80.7%）显著高于BT奖励模型（74.2%）
- 使用LLaMA3-8B-it时，偏好模型在推理任务上达到94.8%，而BT模型为87.8%

---

## 相关工作

### 理论相关工作

| 方向 | 代表工作 | 特点 |
|------|----------|------|
| 基于奖励的RLHF理论 | Xu et al. [73], Novoseller et al. [48], Xiong et al. [72] | 考虑BT模型假设下的在线/离线学习 |
| 通用偏好RLHF | Dudík et al. [22], Wang et al. [65] | 研究一般偏好模型，但未考虑KL正则化 |
| 对抗 bandits | Yue et al. [78], Saha [55] | 偏好反馈的bandit框架 |

### 算法相关工作

- **DPO (Direct Preference Optimization)**: Rafailov et al. [53] - 离线直接偏好优化
- **IPO (Implicit Preference Optimization)**: Azar et al. [3] - 泛化的偏好优化框架
- **Nash Learning**: Munos et al. [46] - 基于Nash均衡的LLM对齐

### 本文与现有工作的区别

1. **框架差异**：采用reverse-KL正则化的minimax博弈，更贴近实际LLM对齐实践
2. **学习范式**：同时考虑离线学习和批量在线迭代学习（稀疏策略更新）
3. **实际实现**：提供了可实际部署的算法近似方案

---

## 方法

### 问题 formulation

定义KL正则化目标函数：
$$J(\pi_1, \pi_2) = \mathbb{E}_{x \sim d_0, a_1 \sim \pi_1, a_2 \sim \pi_2}[P^*(x, a_1, a_2) - \eta^{-1} D_{KL}(\pi_1(\cdot|x) \| \pi_0(\cdot|x)) + \eta^{-1} D_{KL}(\pi_2(\cdot|x) \| \pi_0(\cdot|x))]$$

其中：
- $\pi_0$是初始策略
- $\eta > 0$是KL惩罚系数
- $P^*$是底层真实偏好函数

### Nash均衡定义

$$\pi^* = \arg\max_{\pi_1 \in \Pi} \arg\min_{\pi_2 \in \Pi} J(\pi_1, \pi_2)$$

学习目标是找到$\epsilon$-近似Nash策略$\hat{\pi}_1$：
$$J(\pi_1^*, \pi_2^*) - J(\hat{\pi}_1, \dagger) \leq \epsilon$$

### 离线算法：PELHF (Algorithm 1)

1. 从离线数据集$D_{off}$计算MLE偏好模型$\hat{P}$
2. 构建版本空间（version space）：
$$\tilde{P} = \{P \in \mathcal{P} : \sum_{i=1}^n (P(x_i, a_1^i, a_2^i) - \hat{P}(x_i, a_1^i, a_2^i))^2 \leq \beta^2/2\}$$
3. 求解悲观价值估计下的minimax问题：
$$\hat{\pi}_1 = \arg\max_{\pi_1 \in \Pi} \min_{\pi_2 \in \Pi} \min_{P \in \tilde{P}} \mathbb{E}[P(x, a_1, a_2) + \eta^{-1} \log \frac{\pi_0(a_1|x)}{\pi_1(a_1|x)} - \eta^{-1} \log \frac{\pi_0(a_2|x)}{\pi_2(a_2|x)}]$$

### 在线迭代算法：OELHF (Algorithm 2)

**主要思想**：在每个迭代中，主 agent（max-player）利用已有数据优化策略，增强器（min-player）探索不确定性方向

**算法流程**：
1. **Exploitation**：基于历史数据计算MLE $\hat{P}_t$，求解Nash均衡得到$\hat{\pi}_1^t$
2. **Exploration**：增强器最大化不确定性：
$$\pi_2^t = \arg\max_{\pi_2 \in \Pi} \tilde{\Gamma}_m^t(\lambda, \hat{\pi}_1^t, \pi_2)$$
其中
$$\tilde{\Gamma}_m^t(\lambda, \pi_1, \pi_2) = \sup_{P \in \mathcal{P}} \frac{|\mathbb{E}_{x \sim d_0}[P(x, \pi_1, \pi_2) - \hat{P}_t(x, \pi_1, \pi_2)]|}{\sqrt{\lambda + \frac{1}{m}\sum_{s=1}^{t-1}\sum_{j=1}^m (P(x_{s,j}, a_{1,s,j}, a_{2,s,j}) - \hat{P}_t(x_{s,j}, a_{1,s,j}, a_{2,s,j}))^2}}$$
3. 收集偏好数据并更新

### 关键概念

- **信息比率 (Information Ratio)**：衡量样本外误差与样本内误差的比值
- **eluder系数**：限制从访问过的状态-动作分布泛化到未见部分的能力

---

## 实验设计

### 模型与数据集

- **基础模型**: Zephyr-SFT-7B (基于Mistral-7B-v0.1，在200K Ultra-chat数据上微调)
- **偏好数据集**: Ultra-feedback
  - 训练集: 60K prompts
  - 验证集: 1K prompts
  - 测试集: 3K prompts

### 评估方法

1. **Gold Win Rate**: 使用LLaMA3-8B偏好模型在测试集上进行头对头比较
2. **AlpacaEval2**: 使用GPT-4 Preview作为评判者在分布外数据集上评估

### 对比方法

- SFT (基线)
- Offline DPO
- Offline IPO
- Online ELHF-IPO (本文方法)

### 实现细节

- **主agent近似**: 使用self-play IPO近似Nash均衡预言机
- **增强器实现**: 使用拒绝采样（n=4）生成多样本，通过锦标赛式排序选择最优响应
- **偏好模型构建**: 将偏好对格式化为指令-following任务，微调LLM预测偏好概率

---

## 实验结果

### 表2：不同KL系数下IPO对齐模型的评估

| 模型 | V.S. SFT | V.S. η=0.1 | V.S. η=0.5 | V.S. η=1.0 | AlpacaEval2 |
|------|----------|------------|------------|------------|-------------|
| SFT | 0.5 | 0.121 | 0.205 | 0.231 | 4.63 |
| Offline-IPO-η=0.1 | 0.879 | 0.5 | 0.673 | 0.769 | 9.36 |
| Offline-IPO-η=0.5 | 0.795 | 0.327 | 0.5 | 0.632 | 6.86 |
| Offline-IPO-η=1.0 | 0.710 | 0.230 | 0.328 | 0.5 | 6.55 |

**结论**: η=0.1的IPO模型在所有对比中表现最佳

### 表3：不同RLHF算法的最终评估结果

| 模型 | 设置 | Gold WR | AlpacaEval2 WR |
|------|------|---------|----------------|
| SFT | - | - | 4.63 |
| Offline DPO | 离线 | 0.41 | 9.33 |
| Offline IPO | 离线 | 0.5 | 9.36 |
| Online ELHF-IPO | 在线 | **0.78** | **17.67** |

**关键发现**: Online ELHF-IPO在Gold Win Rate上比Offline DPO提升90%，在AlpacaEval2上提升89%

---

## 结果与分析

### 理论保证

**定理1（离线设置）**: 在一定假设下，算法1输出的策略满足：
$$J(\pi_1^*, \pi_2^*) - J(\hat{\pi}_1, \dagger) \leq 4\beta\sqrt{\frac{C(\pi_1^*, \pi_D, \mathcal{P})}{n}}$$

其中$C(\pi_1^*, \pi_D, \mathcal{P})$是覆盖系数，衡量目标策略与数据收集策略之间的分布偏移。

**定理2（在线设置）**: 对于任意$\epsilon > 0$，存在迭代$t_0$使得：
$$J(\pi_1^*, \pi_2^*) - J(\hat{\pi}_1^{t_0}, \dagger) \leq \epsilon$$

**关键洞察**:
- 离线学习的覆盖系数在实际场景中可能很大（实验显示Gemma-7B-it与Gemma-2B-it的平均KL散度达456.4）
- 在线迭代学习通过主动探索可以有效缓解覆盖不足问题

### 实践意义

1. **通用偏好模型的优势**：偏好模型在推理任务上显著优于BT奖励模型，且具有更好的泛化能力
2. **在线迭代的有效性**：通过主动探索和数据积累，Online ELHF-IPO大幅超越离线方法
3. **KL系数选择**：较小的KL系数（η=0.1）更有利于偏好优化

---

## 未来可能改进方向

### 理论层面

1. **更弱的假设条件**：放宽有限策略类假设，研究无限函数逼近下的收敛性
2. **更紧的样本复杂度**：当前界依赖eluder系数，可探索更宽松的复杂度度量
3. **连续动作空间**：当前假设动作空间离散，考虑连续响应空间的扩展

### 算法层面

1. **计算效率优化**：当前算法需要求解minimax问题，可研究更高效的近似方法
2. **探索策略改进**：探索增强器的设计可以更精细，如使用不确定性估计引导采样
3. **多轮迭代优化**：研究更激进的迭代策略和早停准则

### 应用层面

1. **大规模模型验证**：在更大规模模型（如70B+）上验证方法有效性
2. **多模态扩展**：将框架扩展到图像、视频等多模态生成任务
3. **安全性与对齐**：研究如何利用通用偏好框架更好地实现AI安全对齐

---

## 总结

本文创新性地研究了通用偏好预言机下的RLHF问题，提出了基于反向KL正则化的minimax博弈框架，并设计了样本高效的离线（PELHF）和在线迭代（OELHF）算法。理论分析建立了有限样本收敛保证，实验验证了方法在偏好建模准确率和实际对齐效果上的优势。该工作为超越经典BT模型的RLHF研究开辟了新方向。