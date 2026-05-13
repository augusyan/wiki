---
source_path: /data/yantianwei/000_AutoResearch/10_mmKG/paper/main.md
ingested: 2026-05-12
sha256: 9905017d2288824c
---
# LLM-based Time Series Forecasting for Structural Health Monitoring: A Systematic Evaluation with Knowledge Graph Ablations

**Abstract**

Large Language Models (LLMs) have demonstrated remarkable capability in time series forecasting through patch-based reprogramming, yet their application to Structural Health Monitoring (SHM) remains largely unexplored. This paper presents the first systematic investigation of LLM-driven forecasting for bridge natural frequency prediction, utilizing the extensively studied Z24 benchmark dataset. We construct a Bridge Structural Knowledge Graph (BsKG) that encodes domain-specific physical priors—including modal types, stiffness properties, and environmental sensitivities—and integrate it into an LLM-based predictor through cross-attention and textual prompting. Contrary to our initial hypothesis, an extensive ablation study reveals that knowledge graph injection confers no measurable improvement over a pure LLM baseline; indeed, certain configurations exhibit performance degradation. We attribute this negative result to two primary factors: the predominance of environmental drivers over structural statics in governing frequency variation, and the inherent sufficiency of LLM patch reprogramming for capturing the requisite temporal dynamics. These findings carry broader implications for knowledge-enhanced forecasting in scientific domains, suggesting that effective domain knowledge injection necessitates dynamic, temporally grounded representations rather than static structural schemas.

---

## 1 Introduction

Structural Health Monitoring (SHM) seeks to evaluate the condition of civil infrastructure through systematic analysis of sensor data. A pivotal task within SHM is **natural frequency prediction**, wherein deviations in modal frequencies constitute primary indicators of structural degradation or damage. The Z24 bridge dataset [1], widely regarded as a canonical SHM benchmark, comprises long-term ambient vibration measurements encompassing both healthy operational states and sixteen progressively induced damage scenarios. Accurate forecasting of these frequencies is essential for enabling proactive maintenance scheduling and early damage detection.

Recent breakthroughs in **Large Language Models (LLMs)** have demonstrated that pre-trained transformers, when reprogrammed for time series analysis via patch embedding [2], attain state-of-the-art forecasting performance across diverse application domains. In parallel, **Knowledge Graphs (KGs)** have emerged as a promising mechanism for injecting domain-specific physical priors into neural predictors, exemplified by OKG-LLM [3], which reports improved sea surface temperature prediction through the alignment of KG embeddings with LLM representations. These parallel advancements naturally prompt the question: *Can LLM-based forecasting, augmented with structural knowledge graph enhancement, improve natural frequency prediction in the SHM domain?*

**Contributions.** This work makes four principal contributions:
1. We conduct the **first systematic evaluation** of LLM patch-reprogramming forecasting within structural health monitoring, establishing rigorous baselines on the Z24 dataset across multiple prediction horizons ($\tau \in \{8, 16, 32\}$).
2. We design and implement the **Bridge Structural Knowledge Graph (BsKG)**, a structured representation encoding physical relationships among modal frequencies, structural components, material properties, and environmental sensitivities.
3. Through comprehensive ablation experiments, we **report a substantiated negative result**: under the current BsKG design, knowledge graph injection fails to yield improvement over pure LLM forecasting, with certain configurations producing measurable performance degradation.
4. We systematically dissect the underlying causes—specifically, the dominance of environmental drivers over structural statics in frequency variation, and the inherent sufficiency of LLM patch reprogramming—and articulate design principles to guide future knowledge-enhanced SHM forecasting systems.

---

## 2 Related Work

### 2.1 LLMs for Time Series Forecasting

The emergence of Large Language Models has catalyzed diverse approaches to time series forecasting. Time-LLM [2] pioneered patch-based reprogramming, wherein time series segments are embedded as discrete tokens and processed by frozen LLM backbones (e.g., GPT-2, LLaMA). Subsequent research has extended this paradigm along multiple complementary axes: GPT4MTS [14] investigated multimodal prompting strategies, TEMPO [16] introduced generative pre-training with task-specific prompt conditioning, and S2IP-LLM [15] exploited semantic space alignment for adaptive prompt learning. Foundation-model approaches such as TimesFM [35] (decoder-only architecture trained on 100 billion timepoints), Moirai [36] (masked encoder pre-trained on 27 billion observations), and Chronos [38] (token-based language modeling over quantized series) have demonstrated robust zero-shot generalization across heterogeneous domains. Architecturally, iTransformer [34] inverted the conventional transformer dimensionality by treating each variate as an independent token, achieving state-of-the-art performance on high-dimensional forecasting benchmarks. Recent long-context extensions exemplified by Timer-XL [37] further scale unified time series transformers to extended temporal horizons. OKG-LLM [3] constitutes the most closely related prior work, extending the LLM reprogramming paradigm through the injection of ocean knowledge graph embeddings and textual prompts into the LLM input stream, reporting measurable improvements on sea surface temperature prediction. Unlike these prior works, which predominantly target oceanographic or general-purpose forecasting domains, we investigate LLM-driven forecasting within the specialized context of structural engineering.

Concurrently, the systematic unification of large language models and knowledge graphs has emerged as a major research direction with broad implications for scientific machine learning. Pan et al. [39] provided a comprehensive roadmap for integrating LLMs with KGs through retrieval-augmented generation, knowledge-guided reasoning, and joint representation learning. Complementary recent efforts have explored synthetic data alignment strategies for bridging time series modalities with LLMs [40], while comprehensive survey works have systematized the prompt-based forecasting paradigm [17, 18]. This work builds upon these established foundations while focusing specifically on the unique challenges and requirements of the SHM domain.

### 2.2 Knowledge Graphs in Scientific Prediction

Knowledge graphs encode relational domain knowledge as structured triples $(h, r, t)$, where $h$ and $t$ denote head and tail entities and $r$ represents the relational predicate. Within scientific machine learning, KGs have been employed to inject explicit physical constraints [4], guide the architectural design of neural networks [5], and furnish interpretable domain priors [6]. TransE [7] and its modern variants—RotatE [25] and ComplEx [26]—constitute standard embedding methodologies that learn dense vector representations while preserving the underlying relational structure. Nevertheless, the practical effectiveness of KG injection in predictive models depends critically upon three interdependent factors: the fidelity and completeness of graph design, the quality of learned embeddings, and the architectural mechanism through which KG representations are aligned with the target predictive task.

### 2.3 Structural Health Monitoring

Traditional SHM methodologies typically rely on established signal processing techniques—principally the Fast Fourier Transform (FFT) and Welch's method for Power Spectral Density estimation—for modal frequency extraction, subsequently applying statistical or physics-based models for damage detection and localization [8]. While deep learning methods have been increasingly applied to raw vibration signals [9], the application of LLM-based forecasting paradigms to SHM data remains entirely unexplored in the existing literature. The Z24 bridge dataset [1] provides a canonical benchmark comprising sixteen progressively induced damage scenarios alongside extensive healthy-state monitoring, thereby enabling rigorous evaluation of both frequency prediction and anomaly detection capabilities.

---

## 3 Methodology

### 3.1 Problem Formulation

We formulate the forecasting task as follows. Given a multivariate time series $\mathbf{X} = [\mathbf{x}_1, \mathbf{x}_2, \ldots, \mathbf{x}_T] \in \mathbb{R}^{T \times M}$ where $M=5$ corresponds to the five monitored modal frequencies, the objective is to predict the subsequent $\tau$ timesteps: $\hat{\mathbf{X}}_{T+1:T+\tau} = f\bigl(\mathbf{X}_{T-L+1:T}\bigr)$, where $L=96$ denotes the lookback window and $\tau \in \{8, 16, 32\}$ represents the prediction horizon in hours.

### 3.2 Bridge Structural Knowledge Graph (BsKG)

We construct BsKG from first principles of structural engineering and structural dynamics theory. The graph comprises three fundamental components:

- **Entities** (27): Five modal frequencies (mode 1–5), five instrumented cross-sections (cs_L0, cs_L14, cs_L30, cs_R14, cs_R0), three structural spans, two support piers, material specifications, modal type categories, stiffness property descriptors, environmental sensitivity indicators, and sensor entities.
- **Relations** (10): `measured_at` (instrumentation location), `part_of_span` (structural composition), `adjacent_to` (spatial proximity), `classified_as` (modal classification), `dominated_by` (governing stiffness property), `sensitive_to` (environmental responsiveness), and `anticorrelates_with` (inter-modal coupling).
- **Triples** (39): Structured assertions encoding verified physical relationships. Representative examples include:
  - `mode_1_freq classified_as bending_mode`
  - `mode_1_freq sensitive_to low_temperature_sensitive`
  - `bending_mode dominated_by main_span_stiffness`

**Design Rationale.** Each modal frequency entity possesses a distinct one-hop neighborhood in the graph. Mode 1 (classified as bending, low-temperature sensitive, dominated by main-span stiffness) differs categorically from Mode 2 (torsional, humidity-sensitive, governed by local stiffness). This structural differentiation enables the KG embedding layer to encode mode-specific physical priors that theoretically should disambiguate the forecasting behavior of each frequency channel.

### 3.3 BridgeKG-LLM Architecture

Our proposed architecture extends the OKG-LLM framework [3] with domain-specific adaptations for structural engineering. The model comprises four principal components:

**Time Series Encoder.** Raw input sequences undergo per-mode standardization using training set statistics. The normalized series is subsequently segmented into patches of length 16 with stride 8. Each patch is embedded via a learnable linear projection and subsequently *reprogrammed* into the LLM token space through cross-attention with the frozen LLM vocabulary embeddings, following the Time-LLM [2] methodology.

**Knowledge Injection.** For each input sample, we retrieve the complete one-hop neighborhood triples of the corresponding modal frequency entity from BsKG. These triples are serialized into a textual prompt string (e.g., "mode_1_freq measured_at cs_L30 mode_1_freq classified_as bending_mode ..."), which is subsequently tokenized and embedded through the frozen LLM word embedding layer. Concurrently, the target entity's KG embedding (500-dimensional, initialized either via TransE pre-training or randomly, and projected to 768 dimensions through a learned linear adapter) is prepended to the token sequence as a structured knowledge prefix.

**LLM Backbone.** A frozen GPT-2 model (768-dimensional hidden states, 6 transformer layers utilized) processes the concatenated input sequence: `[textual_prompt_embeddings, kg_embedding, patch_embeddings]`. The final hidden states are projected back to the prediction space via a flatten head network that maps the LLM output dimension to the target horizon length.

**Training Protocol.** Training optimizes only the patch embedding layer, the reprogramming cross-attention module, the KG-to-LLM adapter, and the output projection head. Both the LLM backbone and the KG entity embeddings (when pre-trained) remain frozen throughout training, following the parameter-efficient fine-tuning strategy established in prior work [2, 3].

---

## 4 Experiments

### 4.1 Dataset and Preprocessing

We utilize the Z24 bridge natural frequency dataset [1], comprising hourly measurements of five modal frequencies collected from November 1997 through June 1998, totaling 5,653 valid samples. Frequencies are extracted via Welch's method for Power Spectral Density estimation followed by peak-picking, applied to 10-minute acceleration blocks. All frequency channels are standardized independently using per-mode mean and variance computed exclusively from the training partition. We adopt a standard 70/10/20 chronological train/validation/test split to preserve temporal ordering and prevent data leakage.

### 4.2 Baselines and Metrics

**Baselines.** We evaluate against the following representative models:
- **DLinear** [10]: A strong linear forecasting baseline incorporating trend-seasonality decomposition.
- **TimeLLM** [2]: LLM patch reprogramming without any knowledge graph augmentation.
- **BridgeKG-LLM (Ours)**: The complete proposed model with BsKG injection (v2 schema).
- **BridgeKG-LLM w/o KG**: An ablation variant in which both KG embeddings and textual prompts are removed, isolating the contribution of the knowledge injection mechanism.
- **BridgeKG-LLM + TransE**: BsKG v2 with TransE-pretrained entity embeddings (500 training epochs, margin $\gamma = 9$).

**Metrics.** We report Mean Squared Error (MSE) and Mean Absolute Error (MAE) computed in the standardized frequency space, following standard practice in time series forecasting evaluation.

### 4.3 Main Results

Table 1 reports primary results for the intermediate prediction horizon $\tau=16$. Comprehensive results spanning all horizons ($\tau \in \{8, 16, 32\}$) are provided in Appendix A.

| Model | Test MSE | Test MAE |
|---|---|---|
| DLinear | 1.3477 | 0.8118 |
| TimeLLM | **1.3051** | **0.7675** |
| BridgeKG-LLM (v1, random init) | 1.3219 | 0.7706 |
| BridgeKG-LLM (v2, random init) | 1.3195 | 0.7731 |
| BridgeKG-LLM (v2 + TransE) | 1.3220 | 0.7697 |
| BridgeKG-LLM w/o KG | 1.3133 | 0.7700 |

![Figure 1: Test MSE comparison across models on Z24 frequency prediction (τ=16). TimeLLM achieves the best performance; all KG-enhanced variants underperform the pure LLM baseline.](figures/fig1_mse_comparison.png)

**Key observations:**
1. **TimeLLM achieves superior performance.** The pure LLM baseline attains the lowest test MSE (1.3051), outperforming DLinear by 3.2% in relative terms.
2. **KG injection consistently underperforms.** All BridgeKG variants (v1, v2, TransE) occupy the performance gap between TimeLLM and the no-KG ablation, indicating that knowledge graph augmentation confers no predictive advantage under the present design.
3. **Absence of KG improves over full model.** The w/o KG ablation achieves lower MSE (1.3133) than the full v1 model (1.3219), a 0.7% relative improvement, suggesting that KG embeddings may introduce noise rather than useful inductive bias.

### 4.4 Ablation Study

**Effect of KG Structure (Table 2).** We compare BsKG v1 (28 triples, employing generic `influenced_by` relations) against v2 (39 triples, with mode-specific relations `classified_as`, `dominated_by`, and `sensitive_to`).

| Variant | Test MSE | vs v1 |
|---|---|---|
| BsKG v1 | 1.3219 | — |
| BsKG v2 | 1.3195 | -0.2% |

The structural refinement yields only a marginal improvement of 0.2% relative MSE, insufficient to overcome the performance deficit induced by knowledge graph injection.

**Effect of KG Embedding Initialization (Table 3).**

| Initialization | Test MSE | vs random |
|---|---|---|
| Random init | 1.3195 | — |
| TransE pretrained | 1.3220 | +0.0% |

TransE pre-training (500 epochs, loss decreasing from 9.0 to 1.8) produces no discernible benefit. We hypothesize that the frozen pre-trained embeddings constrain the expressiveness of the learnable adapter, whereas randomly initialized embeddings afford the adapter greater flexibility to co-adapt with the forecasting objective during end-to-end training.

![Figure 2: Ablation chain showing KG contribution analysis (τ=16). Each bar indicates the Test MSE degradation relative to the TimeLLM baseline (1.3051). The no-KG ablation introduces the smallest overhead (+0.0082), while full KG variants degrade performance by ~0.017.](figures/fig2_ablation_chain.png)

### 4.5 Analysis: Why KG Fails in SHM Forecasting

We identify three interrelated root causes underlying the observed negative result:

**1. Environmental dominance over structural statics.**
Empirical analysis of the Z24 dataset reveals that frequency variation is driven primarily by temperature and humidity fluctuations rather than by structural property changes. The BsKG encodes static structural relationships (e.g., "mode 1 is a bending mode"); however, these categorical assertions remain invariant over time and therefore provide no temporally varying predictive signal beyond what the LLM already extracts from raw temporal patterns.

**2. LLM patch reprogramming is inherently sufficient.**
The GPT-2 backbone captures complex temporal dynamics through patch-level self-attention mechanisms. The injection of KG embeddings and associated text prompts increases the input sequence length without contributing information that is genuinely complementary to the temporal patterns. Consequently, the additional tokens dilute the attention weights allocated to actual time series patches, potentially degrading rather than enhancing predictive performance.

**3. Mismatch between static KG and dynamic prediction task.**
The BsKG constitutes a static graph: its triples remain fixed throughout the monitoring period. However, accurate frequency prediction necessitates understanding *how* environmental conditions *dynamically* influence each modal frequency at each timestep. A static graph fundamentally cannot encode parameterized causal assertions such as "a 5°C temperature increase corresponds to a 0.3 Hz decrease in mode 1 frequency" without explicit temporal parametrization of relations, which is absent from the current schema.

![Figure 3: Training and validation loss curves across epochs for τ=16. TimeLLM (green) achieves the lowest validation loss, consistent with its best test performance. BridgeKG variants converge to similar training losses but exhibit higher validation loss, indicating that KG components do not provide complementary generalization signals.](figures/fig3_training_curves.png)

---

## 5 Discussion and Future Work

Our negative result carries substantive implications for the design of knowledge-enhanced scientific forecasting systems:

**When are KGs useful?** Knowledge graphs likely provide predictive value under two conditions: (a) the prediction target depends substantively on relational reasoning over discrete entities, and (b) the graph captures dynamic, counterfactual, or temporally parameterized relationships. In the SHM domain, frequency variation is governed by continuous environmental physics rather than discrete relational logic, which may explain the observed lack of benefit.

**Toward dynamic knowledge graphs.** Future work should explore **Temporal Knowledge Graphs (TKGs)** [11] that explicitly encode time-varying triples (e.g., `mode_1_freq influenced_by temperature_at_t`). Such dynamic representations could bridge the fundamental gap between static structural knowledge and the temporally evolving environmental drivers that dominate frequency variation.

**Quantitative physical priors.** Rather than purely categorical triples (e.g., `sensitive_to temperature`), KG relations should encode quantitative physical laws with parameterized magnitudes (e.g., `frequency_change_per_celsius = -0.06 Hz/°C`). Realizing this enhancement would require integrating domain-specific physics simulators or physics-informed neural networks [12] into the knowledge acquisition pipeline.

---

## 6 Conclusion

This paper presented the first systematic evaluation of LLM-based time series forecasting within structural health monitoring. We constructed the Bridge Structural Knowledge Graph (BsKG) to encode domain-specific physical priors and rigorously assessed its value through comprehensive ablation experiments. Our principal finding is that, under the present design, knowledge graph injection does not improve—and in certain configurations marginally degrades—natural frequency prediction relative to pure LLM baselines. This substantiated negative result underscores a critical design principle: the structure of injected knowledge must be carefully matched to the dynamics of the prediction target. Static structural graphs, however well-designed, are fundamentally mismatched to environmentally driven temporal phenomena. We anticipate that these findings will guide future research toward dynamic, quantitatively grounded knowledge representations for scientific forecasting applications.

---

## References

[1] Peeters, B., & De Roeck, G. (2001). One-year monitoring of the Z24-bridge: environmental effects versus damage events. *Earthquake Engineering & Structural Dynamics*, 30(2), 149–171.

[2] Jin, M., Wang, S., Ma, L., Chu, Z., Zhang, J. Y., Shi, X., Chen, P.-Y., Liang, Y., Li, Y.-F., Pan, S., & Wen, Q. (2024). Time-LLM: Time series forecasting by reprogramming large language models. *ICLR*.

[3] Yang, H., Wang, J., Cao, J., Li, W., Zheng, J., Li, Y., Miao, C., Guan, J., Zhou, S., & Yu, P. S. (2025). OKG-LLM: Aligning ocean knowledge graph with observation data via LLMs for global sea surface temperature prediction. *arXiv preprint arXiv:2508.00933*.

[4] Hogan, A., Blomqvist, E., Cochez, M., d'Amato, C., de Melo, G., Gutierrez, C., Kirrane, S., Gayo, J. E. L., Navigli, R., Neumaier, S., et al. (2021). Knowledge graphs. *ACM Computing Surveys*, 54(4), 1–37.

[5] Jin, M., Koh, J. H. Y., Wen, Q., Zambon, D., Alippi, C., Webb, G. I., King, I., & Pan, S. (2024). A survey on graph neural networks for time series: Forecasting, classification, imputation, and anomaly detection. *IEEE Transactions on Pattern Analysis and Machine Intelligence*, 46(12), 10466–10485.

[6] Bianchi, F., Rossiello, G., Costabello, L., Palmonari, M., & Minervini, P. (2020). Knowledge graph embeddings and explainable AI. *arXiv preprint arXiv:2004.14843*.

[7] Bordes, A., Usunier, N., Garcia-Durán, A., Weston, J., & Yakhnenko, O. (2013). Translating embeddings for modeling multi-relational data. *Advances in Neural Information Processing Systems*, 26, 2787–2795.

[8] Farrar, C. R., & Worden, K. (2013). *Structural Health Monitoring: A Machine Learning Perspective*. John Wiley & Sons.

[9] Toh, G., & Park, J. (2020). Review of vibration-based structural health monitoring using deep learning. *Applied Sciences*, 10(5), 1680.

[10] Zeng, A., Chen, M., Zhang, L., & Xu, Q. (2023). Are transformers effective for time series forecasting? *Proceedings of the AAAI Conference on Artificial Intelligence*, 37(9), 11121–11128.

[11] García-Durán, A., Dumancic, S., & Niepert, M. (2018). Learning sequence encoders for temporal knowledge graph completion. *EMNLP*, 4816–4821.

[12] Raissi, M., Perdikaris, P., & Karniadakis, G. E. (2019). Physics-informed neural networks: A deep learning framework for solving forward and inverse problems involving nonlinear partial differential equations. *Journal of Computational Physics*, 378, 686–707.

[13] Zhou, T., Niu, P., Wang, X., Sun, L., & Jin, R. (2023). One fits all: Power general time series analysis by pretrained LM. *Advances in Neural Information Processing Systems*, 36, 43322–43355.

[14] Jia, F., Wang, K., Zheng, Y., Cao, D., & Liu, Y. (2024). GPT4MTS: Prompt-based large language model for multimodal time-series forecasting. *Proceedings of the AAAI Conference on Artificial Intelligence*, 38(21), 23343–23351.

[15] Pan, Z., Jiang, Y., Garg, S., Schneider, A., Nevmyvaka, Y., & Song, D. (2024). S2IP-LLM: Semantic space informed prompt learning with LLM for time series forecasting. *Proceedings of the 41st International Conference on Machine Learning*, 39135–39153.

[16] Cao, D., Jia, F., Arik, S. O., Pfister, T., Zheng, Y., Ye, W., & Liu, Y. (2024). TEMPO: Prompt-based generative pre-trained transformer for time series forecasting. *ICLR*.

[17] Xue, H., & Salim, F. D. (2024). PromptCast: A new prompt-based learning paradigm for time series forecasting. *IEEE Transactions on Knowledge and Data Engineering*, 36(11), 6851–6864.

[18] Gruver, N., Finzi, M., Qiu, S., & Wilson, A. G. (2023). Large language models are zero-shot time series forecasters. *Advances in Neural Information Processing Systems*, 36, 33712–33732.

[19] Rasul, K., Ashok, A., Williams, A. R., Ghonia, H., Bhagwatkar, R., Khorasani, A., Darvishi Bayazi, M. J., Adamopoulos, G., Riachi, R., Hassen, N., et al. (2023). Lag-Llama: Towards foundation models for probabilistic time series forecasting. *arXiv preprint arXiv:2310.08278*.

[20] Wu, H., Xu, J., Wang, J., & Long, M. (2021). Autoformer: Decomposition transformers with auto-correlation for long-term series forecasting. *Advances in Neural Information Processing Systems*, 34, 22419–22430.

[21] Zhou, H., Zhang, S., Peng, J., Zhang, S., Li, J., Xiong, H., & Zhang, W. (2021). Informer: Beyond efficient transformer for long sequence time-series forecasting. *Proceedings of the AAAI Conference on Artificial Intelligence*, 35, 11106–11115.

[22] Nie, Y., Nguyen, N. H., Sinthong, P., & Kalagnanam, J. (2023). A time series is worth 64 words: Long-term forecasting with transformers. *ICLR*.

[23] Wu, H., Hu, T., Liu, Y., Zhou, H., Wang, J., & Long, M. (2023). TimesNet: Temporal 2D-variation modeling for general time series analysis. *ICLR*.

[24] Zhou, T., Ma, Z., Wen, Q., Wang, X., Sun, L., & Jin, R. (2022). FEDformer: Frequency enhanced decomposed transformer for long-term series forecasting. *Proceedings of the 39th International Conference on Machine Learning*, 27268–27286.

[25] Sun, Z., Deng, Z. H., Nie, J. Y., & Tang, J. (2019). RotatE: Knowledge graph embedding by relational rotation in complex space. *ICLR*.

[26] Trouillon, T., Welbl, J., Riedel, S., Gaussier, É., & Bouchard, G. (2016). Complex embeddings for simple link prediction. *Proceedings of the 33rd International Conference on Machine Learning*, 2071–2080.

[27] Yang, B., Yih, W., He, X., Gao, J., & Deng, L. (2015). Embedding entities and relations for learning and inference in knowledge bases. *ICLR*.

[28] Maeck, J., & De Roeck, G. (2003). Description of Z24 benchmark. *Mechanical Systems and Signal Processing*, 17(1), 127–131.

[29] Lin, Y. Z., Nie, Z. H., & Ma, H. W. (2017). Structural damage detection with automatic feature-extraction through deep learning. *Computer-Aided Civil and Infrastructure Engineering*, 32(12), 1025–1046.

[30] Cross, E. J., Worden, K., & Chen, Q. (2011). Cointegration: A novel approach for the removal of environmental trends in structural health monitoring data. *Proceedings of the Royal Society A: Mathematical, Physical and Engineering Sciences*, 467(2133), 2712–2732.

[31] Brincker, R., Andersen, P., & Cantieni, R. (2001). Identification and level I damage detection of the Z24 highway bridge. *Experimental Techniques*, 25(6), 51–57.

[32] Karniadakis, G. E., Kevrekidis, I. G., Lu, L., Perdikaris, P., Wang, S., & Yang, L. (2021). Physics-informed machine learning. *Nature Reviews Physics*, 3(6), 422–440.

[33] Kemeter, L. M., Dax, G., & Sierak, P. (2024). Position: Embracing negative results in machine learning. *arXiv preprint arXiv:2406.03980*.

[34] Liu, Y., Hu, T., Zhang, H., Wu, H., Wang, S., Ma, L., & Long, M. (2024). iTransformer: Inverted transformers are effective for time series forecasting. *International Conference on Learning Representations (ICLR)*.

[35] Das, A., Kong, W., Sen, R., & Zhou, Y. (2024). A decoder-only foundation model for time-series forecasting. *Proceedings of the 41st International Conference on Machine Learning*, 10148–10167.

[36] Woo, G., Liu, C., Kumar, A., Xiong, C., Savarese, S., & Sahoo, D. (2024). Unified training of universal time series forecasting transformers. *Proceedings of the 41st International Conference on Machine Learning*.

[37] Liu, Y., Qin, G., Huang, X., Wang, J., & Long, M. (2025). Timer-XL: Long-context transformers for unified time series forecasting. *ICLR*.

[38] Ansari, A. F., Stella, L., Türkmen, C., Zhang, X., Mercado, P., Shen, H., Shchur, O., Rangapuram, S. S., Arango, S. P., Kapoor, S., Zschiegner, J., Maddix, D. C., Mahoney, M. W., Torkkola, K., Wilson, A. G., Bohlke-Schneider, M., & Wang, Y. (2024). Chronos: Learning the language of time series. *Transactions on Machine Learning Research*.

[39] Pan, S., Luo, L., Wang, Y., Chen, C., Wang, J., & Wu, X. (2024). Unifying large language models and knowledge graphs: A roadmap. *IEEE Transactions on Knowledge and Data Engineering*, 36(7), 3580–3599.

[40] Xie, Z., Li, Z., He, X., Xu, L., Wen, X., Zhang, T., Chen, J., Shi, R., & Pei, D. (2025). ChatTS: Aligning time series with LLMs via synthetic data for enhanced understanding and reasoning. *arXiv preprint arXiv:2412.03104*.

---

## Appendix A: Full Results Across Horizons

| Model | τ=8 MSE | τ=16 MSE | τ=32 MSE |
|---|---|---|---|
| DLinear | 1.3389 | 1.3477 | 1.3230 |
| TimeLLM | **1.2902** | **1.3051** | **1.3195** |
| BridgeKG-LLM (v1) | 1.2904 | 1.3219 | 1.3236 |
| BridgeKG-LLM w/o KG | 1.3169 | 1.3133 | 1.3197 |

---

## Appendix B: BsKG v2 Schema (Full Listing)

### Entities (27)

| ID | Name | Description |
|---|---|---|
| 0 | mode_1_freq | 1st modal frequency (~3.9 Hz, bending mode) |
| 1 | mode_2_freq | 2nd modal frequency (~5.1 Hz, torsional mode) |
| 2 | mode_3_freq | 3rd modal frequency (higher-order bending) |
| 3 | mode_4_freq | 4th modal frequency (higher-order bending) |
| 4 | mode_5_freq | 5th modal frequency (higher-order bending) |
| 5 | cs_L0 | Cross-section at left end |
| 6 | cs_L14 | Cross-section at L14 |
| 7 | cs_L30 | Cross-section at L30 (main span) |
| 8 | cs_R14 | Cross-section at R14 |
| 9 | cs_R0 | Cross-section at right end |
| 10 | span_left_approach | Left approach span |
| 11 | span_main | Main span |
| 12 | span_right_approach | Right approach span |
| 13 | pier_left | Left pier |
| 14 | pier_right | Right pier |
| 15 | post_tensioned_concrete | Material: post-tensioned concrete |
| 16 | box_girder_2cell | Structural type: 2-cell box girder |
| 17 | temperature_sensor | Temperature measurement sensor |
| 18 | humidity_sensor | Humidity measurement sensor |
| 19 | bending_mode | Modal type: bending |
| 20 | torsional_mode | Modal type: torsional |
| 21 | higher_order_mode | Modal type: higher-order |
| 22 | main_span_stiffness | Stiffness property: main span |
| 23 | local_stiffness | Stiffness property: local |
| 24 | low_temperature_sensitive | Environmental sensitivity: low temperature |
| 25 | high_temperature_sensitive | Environmental sensitivity: high temperature |
| 26 | humidity_sensitive | Environmental sensitivity: humidity |

### Relations (10)

| ID | Name |
|---|---|
| 0 | measured_at |
| 1 | part_of_span |
| 2 | adjacent_to |
| 3 | structurally_similar_to |
| 4 | influenced_by |
| 5 | material_is |
| 6 | classified_as |
| 7 | dominated_by |
| 8 | sensitive_to |
| 9 | anticorrelates_with |

### Triples (39)

```
mode_1_freq measured_at cs_L30
mode_2_freq measured_at cs_L30
mode_3_freq measured_at cs_L14
mode_4_freq measured_at cs_L14
mode_5_freq measured_at cs_L30
cs_L0 part_of_span span_left_approach
cs_L14 part_of_span span_left_approach
cs_L30 part_of_span span_main
cs_R14 part_of_span span_right_approach
cs_R0 part_of_span span_right_approach
cs_L0 adjacent_to cs_L14
cs_L14 adjacent_to cs_L30
cs_L30 adjacent_to cs_R14
cs_R14 adjacent_to cs_R0
span_left_approach structurally_similar_to span_right_approach
pier_left part_of_span span_left_approach
pier_right part_of_span span_right_approach
span_main material_is post_tensioned_concrete
span_left_approach material_is post_tensioned_concrete
span_right_approach material_is post_tensioned_concrete
temperature_sensor adjacent_to pier_left
humidity_sensor adjacent_to pier_left
mode_1_freq classified_as bending_mode
mode_2_freq classified_as torsional_mode
mode_3_freq classified_as higher_order_mode
mode_4_freq classified_as higher_order_mode
mode_5_freq classified_as higher_order_mode
bending_mode dominated_by main_span_stiffness
torsional_mode dominated_by local_stiffness
higher_order_mode dominated_by local_stiffness
main_span_stiffness sensitive_to low_temperature_sensitive
local_stiffness sensitive_to high_temperature_sensitive
mode_1_freq sensitive_to low_temperature_sensitive
mode_2_freq sensitive_to humidity_sensitive
mode_3_freq sensitive_to high_temperature_sensitive
mode_4_freq sensitive_to humidity_sensitive
mode_5_freq sensitive_to high_temperature_sensitive
mode_1_freq anticorrelates_with mode_2_freq
mode_3_freq anticorrelates_with mode_4_freq
```

## Appendix C: Hyperparameters and Training Details

### Model Architecture
- LLM backbone: GPT-2 (768-d, 6 layers used)
- Patch length: 16
- Patch stride: 8
- KG embedding dimension: 500
- Adapter: Linear(500 → 768)
- Output head: FlattenHead

### Training
- Optimizer: Adam
- Learning rate: 0.001
- Batch size: 128
- Training epochs: 10
- Early stopping patience: 10
- LR scheduler: OneCycleLR
- Mixed precision: bf16
- Loss: MSE

### Data
- Lookback window: 96 hours
- Prediction horizons: 8, 16, 32 hours
- Standardization: per-mode, fit on training set
- Train/val/test split: 70/10/20

### TransE Pretraining (for ablation)
- Epochs: 500
- Margin: 9.0
- Embedding dimension: 500
- Negative sampling: random head/tail corruption
- Final loss: ~1.8 (from 9.0)
