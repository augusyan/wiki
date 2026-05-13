---
source_path: /data/yantianwei/000_AutoResearch/10_mmKG/0_重要文献收集/多模态知识图谱进展调研.md
ingested: 2026-05-12
sha256: 683a6c1a884a7312
---
# **2023-2026年多模态知识图谱（MMKG）前沿进展与交叉领域深度调研报告**

## **引言：通向人类级别机器智能的关键阶梯与范式跃迁**

在人工智能的发展历程中，知识图谱（Knowledge Graph, KG）一直扮演着结构化知识表达的核心角色。然而，传统的知识图谱长期局限于纯文本形态，即以三元组（头实体、关系、尾实体）的形式存储结构化数据。随着真实世界数据复杂性的呈指数级增长，纯文本的知识表示已无法满足机器对世界的全面认知。真实世界的信息本质上是多模态的——包含视觉、听觉、空间结构等多维度信号。因此，多模态知识图谱（Multi-modal Knowledge Graph, MMKG）的崛起被学术界广泛视为实现人类级别机器智能（Human-level Machine Intelligence）不可或缺的关键步骤1。

在2023年至2026年期间，计算机视觉（CVPR）、自然语言处理（ACL）、机器学习（NeurIPS、ICLR）以及数据挖掘（KDD、WWW、SIGIR）等顶级交叉领域学术会议见证了MMKG研究的爆炸式增长。这一时期的核心范式发生了根本性的三次跨越：第一，从早期的简单特征拼接，演进为利用大规模视觉-语言模型（VLMs）进行深度跨模态语义对齐，并催生了针对极度不平衡数据的生成式扩充；第二，从针对静态图谱的判别式嵌入学习，转向基于离散扩散模型（Discrete Diffusion Models）和大型基础模型（Foundation Models）的生成式推理与持续学习（Continual Learning）；第三，从孤立的图谱补全任务，迈向与多模态大语言模型（MLLMs）深度融合的检索增强生成（GraphRAG）生态系统，在处理长文档、图表推理及复杂多跳问答中展现出空前的可解释性与精确度4。

本调研报告全面梳理了近三年（至2026年中）全球顶尖学者与实验室（如加州大学圣塔芭芭拉分校 William Yang Wang 团队、斯坦福大学 Jure Leskovec 团队、浙江大学陈华钧与张文团队、电子科技大学邵杰团队等）的顶级会议论文与 arXiv 预印本成果。通过系统性地审视 MMKG 的基础构建、核心推理机制、与大型基础模型的双向赋能，以及在推荐系统与科学发现等下游任务中的应用，本报告深度揭示了多模态知识计算在当下的技术演进脉络及未来的发展蓝图。

## **第一部分：基础理论、基准重构与异构表征学习**

学术界对于多模态与知识图谱的交叉研究，目前已形成两大成熟的理论分支：一方面是 KG 驱动的多模态学习（KG4MM），即利用结构化符号知识来增强多模态任务（如图像分类、视觉问答）；另一方面是多模态驱动的知识图谱研究（MM4KG），即将图谱研究的边界扩展至多模态领域，利用多模态信号丰富实体和关系的语义表示4。近三年的研究重心显著向 MM4KG 倾斜，特别是在解决模态异构性、模态缺失以及高质量基准数据集构建方面取得了突破性进展。

### **1\. 模态不对称性与视觉-语言深度对齐**

真实世界中的 MMKG 天生具有异构性。传统的多模态知识图谱嵌入（KGE）方法（如 IKRL、DKRL 以及早期的 MMKRL）通常假设所有实体都具备均匀分布的模态信息（例如每个实体都同时拥有一段文本描述和一张图像），并倾向于孤立地处理不同模态，导致跨模态对齐微弱8。然而，在真实应用场景中，实体往往面临严重的“模态不对称性”（Modality Asymmetry）问题。例如，在一幅名画的知识图谱中，艺术品实体具有丰富的视觉特征，但其创作流派、展出地点或作者实体可能仅有纯文本描述。

为了突破这一瓶颈，被 WWW 2026 接收的 VL-KGE（Vision-Language Knowledge Graph Embeddings）框架实现了结构化关系建模与视觉-语言预训练特征的深度整合8。VL-KGE 框架不再孤立地处理不同模态，而是利用预训练的 CLIP 或 BLIP-2 模型提供的共享嵌入空间，将视觉特征和文本特征与图结构嵌入融合成统一的实体表示。该模型不仅保留了传统架构（如 TransE、DistMult、ComplEx、RotatE）的关系语义保留特性，更关键的是它支持“单模态推理”，允许仅使用可用模态来表征异构实体。通过在对比学习目标中引入结构化约束，VL-KGE 在 WN9-IMG 以及两个全新构建的细粒度多模态艺术史图谱（WikiArt-MKG-v1 和 WikiArt-MKG-v2）的链接预测任务中，显著超越了传统的单模态方法与零样本 CLIP 模型8。

与此同时，为了在图机器学习领域引入更严谨的视觉评估，斯坦福大学研究团队推出了 MM-GRAPH 综合基准测试（收录于 NeurIPS）。区别于以往依赖低分辨率特征或纯文本节点的数据集，MM-GRAPH 显式地整合了高分辨率的视觉节点特征，覆盖了七个不同规模的异构数据集。这为研究图神经网络（GNNs）在多模态复杂环境下的节点与链路属性预测提供了首个统一的评估试验场，揭示了视觉上下文在提升 GNN 性能中的关键作用11。

### **2\. 生成式补全与长尾模态不平衡的克服**

除了模态完全缺失的问题，模态信息量的不平衡同样是制约 MMKG 性能的顽疾。部分热门实体拥有海量的图文描述，而大量长尾实体则面临视觉信号匮乏的窘境。虽然早期的框架如 AdaMF 或 MACO 试图通过对抗训练（Adversarial Training）和对比学习来缓解数据缺失带来的偏差5，但它们无法凭空创造出缺失的语义细节。

NeurIPS 2025 提出的 LBMKGC（Large model-driven Balanced Multimodal Knowledge Graph Completion）框架为解决这一问题提供了创新的生成式思路13。LBMKGC 摒弃了传统的对抗学习填补策略，转而引入了先进的生成式大视觉模型（如 Stable Diffusion XL）。该框架能够根据图谱中的上下文关系和文本描述，自动为缺乏视觉信息的实体生成高质量的图像特征。随后，该模型通过交叉模态注意力机制（CMoA）动态调整多模态嵌入的对齐权重，以弥合异构模态之间的语义鸿沟。通过区分三元组的感知属性（Perceptual attributes）与概念属性（Conceptual attributes），LBMKGC 实现了结构引导的自适应融合。这种利用扩散模型反哺知识图谱结构的策略，标志着从“被动提取”向“主动生成”的范式转移，大幅提升了模型在极度不平衡数据分布下的泛化能力13。此外，细粒度特征处理也成为趋势，例如预印本方法 MyGO 将离散的模态信息视为细粒度的 Token（Fine-Grained Tokens），为跨模态交互提供了更为细致的维度5。

## **第二部分：图谱的动态演化与持续学习机制**

现实世界的知识是随时间动态演化的（Temporal KG Evolution）。随着新事实的不断涌现，静态的 MMKG 很快会面临知识过时的问题。然而，当对模型进行增量训练时，现有的多模态知识图谱推理模型往往会遭受严重的“灾难性遗忘”（Catastrophic Forgetting）。

### **1\. 结构化协同与多面语义解耦**

针对持续学习（Continual Learning）中的遗忘难题，William Yang Wang 团队在 2026 年的 arXiv 研究中提出了两大重量级框架：CMMKGR 与 MF-CKGE14。

* **CMMKGR（Continual Multimodal Knowledge Graph Reasoning）**：传统的持续知识图谱推理方法多局限于纯结构化三元组，无法充分利用新出现实体的多模态信号。CMMKGR 模型引入了“多模态-结构协同课程学习”（Multimodal-structural collaborative curriculum）机制。该机制根据新三元组与历史图谱的结构连通性以及它们的模态兼容性，动态调度渐进式学习的节奏。同时，模型配备了跨模态知识保留机制，通过维持实体表示的稳定性、关系语义的一致性以及模态锚定（Modality Anchoring）来大幅减轻遗忘现象14。  
* **MF-CKGE（Multi-Faceted Continual KGE）**：在真实场景中，实体的语义具有“多面性”（Multi-faceted），且随时间推移其关系上下文会发生动态演化。强制将实体的新旧知识纠缠在同一个共享嵌入空间中会导致链路预测精度的严重退化。MF-CKGE 提出了从时间与语义双重维度的解耦方案：在离线学习阶段，将不断演化的旧知识与新知识隔离到不同的嵌入子空间中，防止知识纠缠，并采用语义解耦减少冗余以提高空间效率。在实时推理阶段，该框架能够根据查询上下文自适应调整不同实体嵌入的权重，有效压制了与查询无关的噪声15。

在计算机视觉跨领域的持续学习方面，KG-GMM（Knowledge Graph Enhanced Generative Multi-modal model for Class-Incremental Learning）则展现了图谱关系在视觉分类任务中的记忆保留潜力。该模型在学习新类别时，通过构建一个不断进化的知识图谱，利用图谱中的关系信息增强类别标签，并在推理时通过分析生成文本中的实体关系来精确定位旧有类别，在少样本类增量学习（Few-shot CIL）基准上达到了最先进的水平16。

### **2\. 重新审视时间基准的逻辑漏洞**

在探索模型架构的同时，学术界对时序知识图谱（TKG）的评估基准也进行了深刻的反思。研究者指出，包括 YAGO 在内的现有 TKG 数据集存在严重的“共现捷径”（Co-occurrence shortcuts）偏差。许多模型即使完全不使用时间信息，仅通过统计实体共现频率，也能获得超过 0.9 的 Hits@10 得分17。

究其根本，现有数据集忽略了“时间间隔知识”（Time-interval knowledge）的严谨定义以及知识的“过时性”（Obsolescence）。为了提供更精确的演化理解，研究界构建了如 FinWiki（金融领域）、ICEWSWiki（社会政治事件）和 WIKI500K 等新型动态基准。这些数据集严格校准了时间戳知识（事件何时发生）与时间间隔知识（背景条件在何时有效）的相互依存关系，确保了对 TKG 持续学习模型进行公平且无偏的评估17。

| 持续学习挑战 | 传统 TKG 处理方式 | 2025-2026年创新解决方案 | 核心解决机制 |
| :---- | :---- | :---- | :---- |
| **灾难性遗忘与语义纠缠** | 全量重训练、简单参数正则化 | MF-CKGE15 | 将实体的新旧知识分离至独立嵌入子空间，推理时进行查询相关的自适应动态赋权。 |
| **新旧图谱节点的模态融合** | 假设静态图谱结构，忽略多模态新节点 | CMMKGR14 | 引入多模态-结构协同课程学习，依据结构连通性与模态兼容性渐进调度学习任务。 |
| **测试基准的“捷径”偏差** | 依赖纯时间戳事件，易被共现频率破解 | FinWiki, ICEWSWiki17 | 引入严谨的“时间间隔”与“过时性”约束，强制模型学习知识随时间演化的因果关系。 |

## **第三部分：深层推理机制的范式革命——从判别式到生成式**

知识图谱推理是发掘隐含知识、完善图谱结构的核心手段。近年来，图谱补全（Completion）、实体对齐（Alignment）与多跳问答（Multi-hop QA）的技术路径发生了显著的颠覆：基于连续向量的距离度量正被离散扩散模型与大型基础模型所取代。

### **1\. 多模态知识图谱补全（MMKGC）：解耦与离散扩散的引入**

MMKGC 的任务是根据已有的头实体和关系，预测尾实体（或相反）。长期以来，该领域受困于“双目标耦合”的瓶颈：即绝大多数模型试图使用单一的嵌入评分器同时完成全局高召回率的实体检索和局部高精度的细粒度消歧。这两项任务本质上需要不同的归纳偏置（Inductive Biases），强行耦合往往导致局部歧义难以消除18。

为此，RADD（Retrieval-Augmented Discrete Diffusion）框架彻底重构了 MMKGC 的系统架构，将其解耦为一个两阶段过程：全局检索与离散扩散重排序18。

* **关系感知多模态检索器（Retriever）**：第一阶段采用基于复数空间（类似于 RotatE）的评分函数。该阶段通过关系门控机制（Relation Gate）融合实体的结构、视觉和文本嵌入。门控机制允许模型根据特定关系的需要（如预测“出生地”可能偏向文本，而预测“画作风格”可能偏向视觉），动态提高最具信息量模态的权重。随后，系统在完整的全实体集合中进行快速闭式评分，输出 Top-K 的粗筛列表。  
* **条件离散去噪器（Denoiser）**：第二阶段放弃了连续空间的距离度量，创新性地引入了离散扩散概率模型（D3PM）。区别于传统对连续向量去噪的扩散模型，RADD 直接在离散的实体索引（Entity Identities）空间执行去噪恢复。去噪器以检索器生成的联合表示作为上下文条件，从带掩码（Mask）的噪声节点出发，通过逐步推理恢复出最准确的实体索引。这种严格限制在 Top-K 短列表内的重排序，既避免了对全词表的稀疏性搜索，又确保了高精度的多模态语义拟合19。

在探索统一表征的路线上，浙江大学团队提出的 TOFU（Token-based Foundation model）展示了强大的跨图谱泛化能力21。现有的绝大部分 MMKGR 方法局限于直推式（Transductive）设置，即为特定数据集学习绑定的嵌入，难以泛化到包含全新实体或关系的未知图谱。TOFU 将基础模型架构引入图谱，它将结构、视觉和文本信息统一离散化为特定模态的 Token，并采用混合消息机制（Mixture-of-message）的层次化融合架构来处理这些 Token。实验证明，该模型在 17 个不同的直推式、归纳式（Inductive）甚至完全归纳式图谱数据集上展现了卓越的“Zero-shot”跨图谱迁移能力，彻底超越了传统依赖特定数据集训练的基线模型22。同时，诸如 SNAG 等轻量级模型（仅 13M 参数）通过在 Transformer 架构中引入实体级别的模态噪声掩码（Noise Masking），巧妙结合了早融合的深度交互特性与晚融合的灵活性，同时支持图谱补全与实体对齐两项任务，展示了极高的参数效率23。

### **2\. 跨图谱实体对齐（MMEA）中“信息鸿沟”的生成式弥合**

实体对齐旨在识别不同 MMKG 中代表现实世界同一对象的实体。阻碍该任务的核心障碍在于不同图谱之间存在巨大的“信息鸿沟”（Information Gaps），即同一实体在不同来源的图谱中，其邻居拓扑结构和可用模态属性往往存在严重不对称25。

为了解决结构与特征的极度不匹配，最新发表于 AAAI 的 MICEA 等生成式对齐方法，突破了仅仅依赖图神经网络聚合局部邻居的传统思路25。该框架利用大型语言模型（LLMs）的零样本合成能力，通过精心设计的提示工程（Prompt Engineering），指导 LLM 合成目标实体缺失的邻居实体与关系属性。借助 LLM 在海量预训练数据中产生的涌现知识，模型能够在进入对齐网络前，人为地补齐两个待对齐实体的局部上下文，确保其达到高度的结构与语义一致性。在最终的相似度计算阶段，模型进一步引入语义一致性、结构一致性和因果约束等多重评估指标来过滤不合理的生成事实，从而在跨语言、跨模态的极端恶劣条件下取得了极高的对齐精度25。与之相辅相成的是 ASGEA 模型，通过从已对齐的子图中挖掘逻辑规则进行推理，进一步强化了基于逻辑的图对齐效果5。

### **3\. 多跳问答（Multi-hop KGQA）的经验主义推理**

多跳推理要求模型在复杂的图结构中进行多次相互关联的关系跳转。现有的基于 LLM 的图谱问答模型（如常见的 Retrieve-then-read 范式）存在严重缺陷：在迭代检索的序列化过程中，LLM 缺乏对先前探索轨迹的感知与记忆，导致推理路径容易随着跳数的增加而偏离初始问题意图，引发大量的冗余探索和逻辑碎片化26。

为打破这一局限，2026 年最新提出的 TRACE（Trajectory-aware Reasoning with Adaptive Context and Exploration priors）构建了一种基于“经验主义”的自主学习推理框架26。该框架通过三个核心机制彻底重构了图谱遍历的逻辑：

1. **动态上下文生成**：在每一次推理跳转时，TRACE 使用生成器将当前的图结构关系路径直接翻译为连贯的自然语言叙事（例如“已知 X 是电影的导演，现在需要寻找该导演的出生地”）。这种动态更新的叙事确保了每一步决策都牢牢锚定在问题的核心意图和累积的历史上下文中27。  
2. **探索泛化与经验先验**：当某条推理路径因达到跳数上限或陷入死胡同而终止时，系统会自动对其进行语义总结。这些成功的或失败的探索轨迹被提炼为可复用的“经验先验”（Experiential Priors）。这种记忆机制使得模型能够识别图谱中的常见陷阱，并在未来的查询中主动规避冗余的路径搜索27。  
3. **双重反馈重排序**：在候选路径生成后，LLM 检索器结合当前的动态上下文与历史经验先验，对生成的候选关系进行双向重排序。相较于完全依赖黑盒大语言模型直接生成答案（极易产生幻觉），TRACE 严格遵循底层的结构化事实，在 WebQSP 和 CWQ 等复杂多跳问答基准测试中，显著超越了 KV-Mem 和 EmbedKGQA 等经典语义解析基线27。

## **第四部分：MMKG与大型多模态模型（MLLMs）的双向赋能与检索增强（GraphRAG）**

如果说前两年的研究重点是“如何将多模态数据高效嵌入图结构”，那么近一年（2025-2026）的绝对产业与学术焦点，则转移到了“MMKG与大型多模态语言模型（LMMs/MLLMs）的深度协同系统”上。MLLMs（如支持任意模态输入输出无缝切换的 NExT-GPT 架构28）虽然在常识推理和跨模态生成上展现了惊人的能力，但它们不可避免地受到“幻觉”（Hallucination）和知识边界模糊的致命困扰29。

### **1\. 跨越文本惯性：引入多模态事实锚定**

在多模态推理中，大型模型面临一种特定的失败模式——“文本惯性”（Textual Inertia）陷阱。一项针对多模态推理链（Chain-of-Thought）鲁棒性评估的 LogicGraph Perturbation Protocol 揭示：当模型的思考链路中出现早期的文本幻觉时，模型会倾向于盲目遵循这条错误的文本逻辑继续生成，完全无视输入图像中存在的、能够纠正错误的明显视觉证据。统计表明，在这种模态冲突的扰动下，现有主流 MLLMs 的自我纠错成功率不到 10%30。这一发现凸显了将外部多模态知识图谱作为确定的、抗干扰的“外部事实锚点”注入大模型生成流程的迫切性。

### **2\. 多模态 GraphRAG 的全面崛起**

为了提供可追溯、可验证且规避文本惯性的知识基础，业界引领了基于图谱的检索增强生成（GraphRAG）技术的演进7。传统的 GraphRAG 系统仅构建于纯文本节点之上，无法理解文档中的图表、公式和空间布局。2025年至2026年涌现的 MMGraphRAG 与 MegaRAG 系统，成功将多模态视觉线索彻底融入了图谱构建、检索与生成流水线6。

* **MMGraphRAG 架构**：传统的视觉融合检索往往将图文直接映射到共享的高维嵌入空间。研究指出，这种做法极易受噪声影响，并导致“语义扁平化”（Semantic Flattening），丢失了图像内部物体之间细粒度的交互知识。为此，MMGraphRAG 利用视觉场景图（Visual Scene Graphs）提取图像结构，并通过一种基于谱聚类（Spectral Clustering）的跨模态实体链接算法（SpecLink），精确实现视觉节点与文本 KG 的融合。在针对复杂多模态图表推理的 DocBench 和 MMLongBench 基准测试中，MMGraphRAG 达到了 76.8% 和 38.8% 的极高准确率，不仅碾压了朴素的 VectorRAG，更因其保留了清晰的跨模态推导路径，在可解释性上具备压倒性优势32。  
* **MegaRAG 的多级提炼机制**：为解决包含超长文本、图表和图像的领域特定文档（如整本学术书籍或分析报告）的理解难题，MegaRAG 重新设计了跨模态知识抽取流程。它不再依赖简单的文本分块提取，而是直接利用 MLLM 对整个多模态页面进行统一的初始特征提取以构建基础图谱。由于初始提取不可避免会遗漏长尾跨模态实体，MegaRAG 引入了上下文特定的局部子图构建与图谱丰富化（Refinement）阶段。通过提取轻量级的页面级子图供大模型进行二次验证，系统在有限的上下文窗口内实现了局部视觉细节与全局语义逻辑的完美统一33。  
* **微软边缘计算视角的 LazyGraphRAG**：为了在算力受限的边缘设备上部署复杂的 GraphRAG，微软研究院提出了延迟图计算（LazyGraphRAG）策略。该策略将耗时昂贵的 LLM 总结操作从离线的知识提取阶段转移到了在线查询阶段，在索引时仅使用传统的 NLP 方法提取概念，极大降低了计算成本，满足了实时交互的严苛需求37。

### **3\. 全局视野下的指令自适应推理（MIAoG）**

现有的基于知识图谱增强的 LLM 推理多采用“查询驱动”（Query-driven）的局部探索策略。这种“走一步看一步”的贪心算法常常因缺乏全局视野而陷入局部最优，无法捕获语义上相距较远但对最终答案至关重要的线索。

被 EACL 2026 接收的 MIAoG（Multi-view Instructed Adaptive reasoning of LLM on KG）框架彻底改变了图谱漫游的决策机制38。MIAoG 在检索开始前，首先强迫 LLM 根据当前用户问题，生成一个多视角指令集（Multi-view instruction set），该指令集显式规划出几条潜在的全局推理意图和轨迹。在具体的知识图谱节点游走过程中，系统激活了实时内省机制（Introspection Mechanism）。每向前探索一层，模型都会自动评估当前路径与全局多视角指令的对齐程度；一旦发现轨迹偏离，立刻自适应地进行路径剪枝。这种以全局指令约束局部搜索的范式，突破了多跳推理中准确率与探索效率不可兼得的“双重瓶颈”38。

| RAG 范式演进 | 底层数据组织形态 | 检索匹配粒度与机制 | 核心推理优势与适用场景 |
| :---- | :---- | :---- | :---- |
| **Vector RAG (传统)** | 扁平化文档块（Chunks）的独立密集向量 | 向量空间中的词汇和语义近似距离匹配 (K-NN) | 实现简单，适用于单文档、基于字面事实的直接问答，响应速度极快。 |
| **Textual GraphRAG** | 基于实体、关系三元组以及聚类社区总结的纯文本图网络 | 结合向量检索与图拓扑遍历，提取子图及层级社区摘要 | 解决跨文档实体的逻辑串联问题，极其擅长全局长文本主题提炼与信息综合。 |
| **MMGraphRAG / MegaRAG** | 融合视觉场景图与图文对齐节点的多模态知识网络 | 跨模态结构化关系检索、谱聚类实体链接、页面级子图自适应扩展 | 能够理解复杂图表、跨模态指代消解，提供高度可解释的跨模态决策依据。 |

## **第五部分：高影响力的下游产业应用**

多模态知识图谱的工程化在近几年取得了突飞猛进的发展，其实际应用已深植于推荐系统、科学发现（AI for Science）及垂直领域的自动化运维等关键场景。

### **1\. 推荐系统中的多维偏好认知与可信度保障**

现代电商、内容社区与本地生活服务平台充斥着极其丰富的多模态信息。通过 MMKG 将用户行为轨迹与物品的视觉、文本语义相连，成为了新一代推荐系统的标配。电子科技大学邵杰教授团队在该领域做出了卓越贡献30。

* **跨模态时空交互**：在兴趣点（POI）推荐这类需要考量时空依赖的任务中，传统序列模型难以捕捉复杂语义。M4REC 模型（KDD 2025）构建了一个整合了用户-POI 轨迹、用户-类别序列以及对应位置的多模态信息的超大知识图谱。其内部设计的基于图谱的关系感知网络，能够自适应地量化时间间隔、空间距离和模态语义这三种截然不同的邻域信息对用户决策的差异化影响，大幅缓解了冷启动与交互稀疏性问题43。同样，诸如 MTKGHAT 和 CrossGMMI-DUKGLR 框架，通过引入多头图注意力和跨模态注意力机制，捕捉到了用户短期动态兴趣与长期结构偏好的精妙平衡44。  
* **构建可信赖的推荐环境**：2026 年的研究直击推荐系统部署的软肋——“模态污染”。在商业环境中，诸如“点击诱饵”图片或“图文不符”的误导性信息比比皆是。如果将这些不可靠的视觉特征盲目注入 MMKG，会导致推荐准确率的断崖式下跌。为此，研究团队提出了一种“即插即用”的模态级修正组件（Modality-level Rectification）。该组件采用轻量级的投影网络，并结合基于 Sinkhorn 的软匹配（Soft Matching）算法，在物品的协同过滤交互特征与多模态内容特征之间动态学习软对应关系。该机制巧妙地抑制了那些语义不一致的错误信号，确保了推荐系统的鲁棒性与可信度，而无需对底层的主干图谱网络进行任何架构修改41。

### **2\. 人工智能驱动的科学发现（AI for Science）与工业应用**

在硬核科学领域的知识表达中，跨越文本、图表和公式的多维信息提取是打通科研数据孤岛的关键。

* **生物医药与化学材料**：生物医学文献往往蕴含大量深埋于图表和长尾段落中的数据。借助预训练的本体蛋白质表示（OntoProtein）或基于化学元素知识图谱的分子对比学习，研究人员在药物-药物相互作用（DDI）预测及蛋白质结构动态分析上展现出远超传统基于纯一维序列特征预测的精度5。同时，利用诸如 DeepKE 这样结合了提示工程和 RAG 管线的多模态大模型抽取工具，材料科学家能够自动化地从数万篇由于 OCR 识别噪音导致的非结构化 PDF 预印本文献中，高精度析取带隙（Bandgap）等核心物理参数，极大地加速了新材料的发现进程46。  
* **复杂系统智能运维（O\&M）**：在海上风电和光伏等大型工业场景中，设备手册、巡检报告以及传感器环境数据构成了一个高度异构的知识池。通过联合文档预处理与面向领域微调的大语言模型实体提取技术，研究者成功将满是印章、手写签名以及光学字符识别（OCR）噪声的原始扫描件转换为具备高度连通性的运维知识图谱。这种高连通性彻底消除了信息检索中的“数据岛屿”（Island Phenomenon）效应，使得系统在诊断设备故障时具备了可靠的逻辑溯源能力，显著降低了由大模型直接问答产生的幻觉风险，为数字孪生系统的落地提供了核心语义引擎49。在这一过程中，多模态事件抽取（Visual/Video EE）技术的发展，使得直接从工业监控视频和音频信号中抽取结构化事件信息成为可能，进一步拓宽了 MMKG 的数据摄入渠道51。

## **第六部分：开源生态、系统基建与社区演进**

多模态知识图谱的学术繁荣与工业界开源生态的狂飙突进密不可分。随着数据规模从百万级迈向数十亿级节点，底层计算库、自动化构建工具与公共图谱资源池经历了全面的迭代。

### **1\. 自动化图谱构建模型的爆发**

得益于新一代开源大型语言模型的推理与工具调用（Tool Calling）能力的增强，从非结构化多模态数据中自动提取图谱的门槛被大幅降低52。

* **DeepSeek-R1**：依托 164K 的超长上下文窗口以及通过强化学习（RL）激发的深度逻辑推理能力，DeepSeek-R1 成为了处理复杂实体关系抽取、代码库分析以及跨页文档关联的最强开源大脑52。  
* **Qwen3-235B**：作为支持双模式（思考模式进行复杂逻辑推理，非思考模式进行高效处理）的强大混合智能体，结合其原生的多语言支持（100+语言），极大地便利了跨语言 MMKG 的全球化知识协同构建52。 配合 Neo4j 发布的 LLM Knowledge Graph Builder 以及 LanceDB 等向量图谱混合检索数据库的支持，开发者现在可以通过简短的 API 调用，实现从 PDF 解析、分块、多模态 Embedding 计算、实体关系提取到词法图谱和实体图谱融合索引的全自动化流水线部署7。

### **2\. 底层图计算框架的极致优化**

为了支撑海量节点的消息传递计算，业界标准的图神经网络框架 PyTorch Geometric (PyG) 迎来了重大架构升级。最新的 PyG 版本针对异构图（Heterogeneous Graphs）、多模态图和动态时序图引入了原生数据类型支持。更为关键的是，通过内置的高效张量运算 API（如 pyg-lib 和 torch-sparse）以及全新优化的分布式图采样（Graph Sampling）与特征存储逻辑，现代 MMKG 模型可以轻松跨越多 GPU 乃至集群环境，在包含数十亿节点的超大规模真实业务网络上进行高效训练与推理55。

### **3\. OpenKG 与去中心化知识众包**

在中国知识图谱社区，由浙江大学、清华大学等学术机构主导的 OpenKG 联盟发挥了中流砥柱的作用。该平台不仅开源了大量高质量的中文图谱和构建工具，还在垂直领域（如涵盖概念、流行病学调查、预防手段等多维子图的 OpenKG-COVID19）的本体建模规范上树立了标杆。为了解决大规模图谱构建过度依赖人力且难以确权的问题，社区创新性地引入了基于区块链架构的 OpenKG Chain。通过共识机制和分布式账本，该平台实现了全球首个三元组/实体级别的知识产权确权与激励，极大地激发了研究者与公众共同维护、扩充多模态知识基地的热情，标志着众包图谱向可信与可持续化方向迈出了关键一步57。

## **结论与未来展望**

综上所述，2023年至2026年期间多模态知识图谱（MMKG）的理论研究与工程实践取得了极其深刻的进展。本报告的深度调研揭示了主导该领域演进的三大决定性趋势：

1. **推理底座的范式革命：从连续判别到离散生成**。传统的图谱研究曾长期受困于在连续高维空间中进行静态距离度量。如今，在以 RADD 为代表的离散扩散模型和以 TOFU 为代表的基础模型推动下，图谱补全与对齐已经完全跨入生成式推理时代。这些框架赋予了模型在极大规模且严重不平衡模态下精准锁定实体、并实现跨图谱“Zero-shot”泛化的空前能力。  
2. **正视真实世界的残缺：时间演化、模态丢失与抗噪建模成为主流**。早期的研究习惯于在“静态网络”和“完美对齐模态”的温室数据中测试模型。而近两年的前沿工作（如 CMMKGR、MF-CKGE、LBMKGC、M4REC 等）直面了真实工业场景中最棘手的难题：跨模态特征不对称、长尾视觉信息缺失、时间衰变引发的灾难性遗忘以及充满恶意的模态噪音。这些极其务实的建模维度的引入，标志着 MMKG 技术从实验室走向了真正的产业可用状态。  
3. **神经符号计算的巅峰：MMKG 与大语言模型（MLLMs）的深度内生耦合**。在当前的认知智能架构中，纯粹离散的结构化图谱或单纯连接主义的黑盒大模型都已显现出能力的玻璃天花板。大语言模型凭借其不可思议的泛化与涌现能力，充当了多模态图谱自动构建、复杂路径规划（如 MIAoG、TRACE 的意图控制）和零样本对齐的“超级大脑”；而 MMKG 则通过多层次的检索增强范式（如 MMGraphRAG、MegaRAG），彻底化身为锚定 MLLM 事实底座、根除“文本惯性”及视觉幻觉的“理性基石”。

展望未来，随着基于流匹配（Flow Matching）等更先进生成技术的融合，以及针对复杂推理任务的自主代理（Agentic AI）在动态图拓扑上的深度应用，多模态知识图谱必将在一个高度不确定、开放且多变的物理世界中，为从自动驾驶具身认知到精密医疗多模态联合诊断等关键人类使命，提供极具逻辑穿透力与坚实可解释性的下一代人工智能基础设施支撑。

#### **引用的著作**

1. Multi-Modal Knowledge Graph Construction and Application: A Survey, [https://www.computer.org/csdl/journal/tk/2024/02/09961954/1IxvQL6EdkQ](https://www.computer.org/csdl/journal/tk/2024/02/09961954/1IxvQL6EdkQ)  
2. Building and Querying Multimodal Knowledge Graphs: A Survey \- ResearchGate, [https://www.researchgate.net/publication/403844081\_Building\_and\_Querying\_Multimodal\_Knowledge\_Graphs\_A\_Survey](https://www.researchgate.net/publication/403844081_Building_and_Querying_Multimodal_Knowledge_Graphs_A_Survey)  
3. Machine Learning and Knowledge Discovery in Databases: European Conference, ECML PKDD 2022, Grenoble, France, September 19–23, 2022, Proceedings, Part II 9783031263903, 3031263901 \- DOKUMEN.PUB, [https://dokumen.pub/machine-learning-and-knowledge-discovery-in-databases-european-conference-ecml-pkdd-2022-grenoble-france-september-1923-2022-proceedings-part-ii-9783031263903-3031263901.html](https://dokumen.pub/machine-learning-and-knowledge-discovery-in-databases-european-conference-ecml-pkdd-2022-grenoble-france-september-1923-2022-proceedings-part-ii-9783031263903-3031263901.html)  
4. \[2402.05391\] Knowledge Graphs Meet Multi-Modal Learning: A Comprehensive Survey, [https://arxiv.org/abs/2402.05391](https://arxiv.org/abs/2402.05391)  
5. zjukg/KG-MM-Survey: Knowledge Graphs Meet Multi-Modal Learning \- GitHub, [https://github.com/zjukg/KG-MM-Survey](https://github.com/zjukg/KG-MM-Survey)  
6. (PDF) A unified multimodal GenAI platform integrating GraphRAG multi-agent systems and custom language models for intelligent document processing and knowledge synthesis \- ResearchGate, [https://www.researchgate.net/publication/403523421\_A\_unified\_multimodal\_GenAI\_platform\_integrating\_GraphRAG\_multi-agent\_systems\_and\_custom\_language\_models\_for\_intelligent\_document\_processing\_and\_knowledge\_synthesis](https://www.researchgate.net/publication/403523421_A_unified_multimodal_GenAI_platform_integrating_GraphRAG_multi-agent_systems_and_custom_language_models_for_intelligent_document_processing_and_knowledge_synthesis)  
7. GraphRAG: Hierarchical Approach to Retrieval-Augmented Generation \- LanceDB, [https://www.lancedb.com/blog/graphrag-hierarchical-approach-to-retrieval-augmented-generation](https://www.lancedb.com/blog/graphrag-hierarchical-approach-to-retrieval-augmented-generation)  
8. VL-KGE: Vision–Language Models Meet Knowledge Graph Embeddings \- arXiv, [https://arxiv.org/html/2603.02435v1](https://arxiv.org/html/2603.02435v1)  
9. VL-KGE: Vision-Language Models Meet Knowledge Graph Embeddings \- arXiv, [https://arxiv.org/abs/2603.02435](https://arxiv.org/abs/2603.02435)  
10. (PDF) VL-KGE: Vision-Language Models Meet Knowledge Graph Embeddings, [https://www.researchgate.net/publication/401524185\_VL-KGE\_Vision-Language\_Models\_Meet\_Knowledge\_Graph\_Embeddings](https://www.researchgate.net/publication/401524185_VL-KGE_Vision-Language_Models_Meet_Knowledge_Graph_Embeddings)  
11. arXiv:2406.16321v2 \[cs.LG\] 30 Mar 2025, [https://arxiv.org/pdf/2406.16321](https://arxiv.org/pdf/2406.16321)  
12. Collaboration of Fusion and Independence: Hypercomplex-driven Robust Multi-Modal Knowledge Graph Completion \- arXiv, [https://arxiv.org/html/2509.23714v2](https://arxiv.org/html/2509.23714v2)  
13. LBMKGC: Large Model-Driven Balanced Multimodal Knowledge Graph Completion, [https://neurips.cc/virtual/2025/poster/117328](https://neurips.cc/virtual/2025/poster/117328)  
14. When Modalities Remember: Continual Learning for Multimodal Knowledge Graphs \- arXiv, [https://arxiv.org/pdf/2604.02778](https://arxiv.org/pdf/2604.02778)  
15. Multi-Faceted Continual Knowledge Graph Embedding for Semantic-Aware Link Prediction, [https://arxiv.org/html/2604.10947v1](https://arxiv.org/html/2604.10947v1)  
16. \[2503.18403\] Knowledge Graph Enhanced Generative Multi-modal Models for Class-Incremental Learning \- arXiv, [https://arxiv.org/abs/2503.18403](https://arxiv.org/abs/2503.18403)  
17. Towards Better Evolution Modeling for Temporal Knowledge Graphs \- arXiv, [https://arxiv.org/html/2602.08353v1](https://arxiv.org/html/2602.08353v1)  
18. RADD: Retrieval-Augmented Discrete Diffusion for Multi-Modal Knowledge Graph Completion \- arXiv, [https://arxiv.org/html/2604.25693v1](https://arxiv.org/html/2604.25693v1)  
19. RADD: Retrieval-Augmented Discrete Diffusion for Multi-Modal Knowledge Graph Completion \- arXiv, [https://arxiv.org/pdf/2604.25693](https://arxiv.org/pdf/2604.25693)  
20. [https://arxiv.org/abs/2604.25693](https://arxiv.org/abs/2604.25693)  
21. Every Little Helps: Building Knowledge Graph Foundation Model with Fine-grained Transferable Multi-modal Tokens \- ResearchGate, [https://www.researchgate.net/publication/400929769\_Every\_Little\_Helps\_Building\_Knowledge\_Graph\_Foundation\_Model\_with\_Fine-grained\_Transferable\_Multi-modal\_Tokens](https://www.researchgate.net/publication/400929769_Every_Little_Helps_Building_Knowledge_Graph_Foundation_Model_with_Fine-grained_Transferable_Multi-modal_Tokens)  
22. \[2602.15896\] Every Little Helps: Building Knowledge Graph Foundation Model with Fine-grained Transferable Multi-modal Tokens \- arXiv, [https://arxiv.org/abs/2602.15896](https://arxiv.org/abs/2602.15896)  
23. GitHub \- zjukg/SNAG: \[Paper\]\[COLING 2025\] Noise-powered Multi-modal Knowledge Graph Representation Framework, [https://github.com/zjukg/SNAG](https://github.com/zjukg/SNAG)  
24. Noise-powered Multi-modal Knowledge Graph Representation Framework \- ACL Anthology, [https://aclanthology.org/2025.coling-main.11.pdf](https://aclanthology.org/2025.coling-main.11.pdf)  
25. Multi-Modal Fact Knowledge Generation for Imbalanced Cross-Source Entity Alignment, [https://ojs.aaai.org/index.php/AAAI/article/view/38999/42961](https://ojs.aaai.org/index.php/AAAI/article/view/38999/42961)  
26. TRACE: An Experiential Framework for Coherent Multi-hop Knowledge Graph Question Answering \- arXiv, [https://arxiv.org/pdf/2604.11193](https://arxiv.org/pdf/2604.11193)  
27. [https://arxiv.org/abs/2604.11193](https://arxiv.org/abs/2604.11193)  
28. Daily Papers \- Hugging Face, [https://huggingface.co/papers?q=cross-modal%20semantic%20understanding](https://huggingface.co/papers?q=cross-modal+semantic+understanding)  
29. MMKG-RAG: Retrieval-Augmented Generation with Multi-modal Knowledge Graph | Request PDF \- ResearchGate, [https://www.researchgate.net/publication/399373231\_MMKG-RAG\_Retrieval-Augmented\_Generation\_with\_Multi-modal\_Knowledge\_Graph](https://www.researchgate.net/publication/399373231_MMKG-RAG_Retrieval-Augmented_Generation_with_Multi-modal_Knowledge_Graph)  
30. Analyzing Reasoning Consistency in Large Multimodal Models under Cross-Modal Conflicts \- arXiv, [https://arxiv.org/pdf/2601.04073](https://arxiv.org/pdf/2601.04073)  
31. Knowledge Graph-Enhanced RAG for Enterprise Question-Answering Systems \- Lund University Publications, [https://lup.lub.lu.se/student-papers/record/9223345/file/9223346.pdf](https://lup.lub.lu.se/student-papers/record/9223345/file/9223346.pdf)  
32. \[2507.20804\] MMGraphRAG: Bridging Vision and Language with Interpretable Multimodal Knowledge Graphs \- arXiv, [https://arxiv.org/abs/2507.20804](https://arxiv.org/abs/2507.20804)  
33. \[2512.20626\] MegaRAG: Multimodal Knowledge Graph-Based Retrieval Augmented Generation \- arXiv, [https://arxiv.org/abs/2512.20626](https://arxiv.org/abs/2512.20626)  
34. MMGraphRAG: Bridging Vision and Language with Interpretable Multimodal Knowledge Graphs \- arXiv, [https://arxiv.org/html/2507.20804v2](https://arxiv.org/html/2507.20804v2)  
35. MegaRAG: Multimodal Knowledge Graph-Based Retrieval Augmented Generation \- arXiv, [https://arxiv.org/html/2512.20626v2](https://arxiv.org/html/2512.20626v2)  
36. UnWeaving the knots of GraphRAG \- turns out VectorRAG is almost enough \- arXiv, [https://arxiv.org/html/2603.29875v2](https://arxiv.org/html/2603.29875v2)  
37. GSoC 2026 – Interest in Project \#36 \- Agentic GraphRAG · openvinotoolkit openvino · Discussion \#34671 \- GitHub, [https://github.com/openvinotoolkit/openvino/discussions/34671](https://github.com/openvinotoolkit/openvino/discussions/34671)  
38. Thinking Beyond the Local: Multi-View Instructed Adaptive Reasoning in KG-Enhanced LLMs \- ACL Anthology, [https://aclanthology.org/2026.findings-eacl.325.pdf](https://aclanthology.org/2026.findings-eacl.325.pdf)  
39. Jie Shao's Homepage, [https://cfm.uestc.edu.cn/\~shaojie/](https://cfm.uestc.edu.cn/~shaojie/)  
40. 导师个人信息 \- 电子科大研招网, [https://yjsjy.uestc.edu.cn/gmis/jcsjgl/dsfc/dsgrjj/11789?yxsh=08](https://yjsjy.uestc.edu.cn/gmis/jcsjgl/dsfc/dsgrjj/11789?yxsh=08)  
41. Towards Trustworthy Multimodal Recommendation \- arXiv, [https://arxiv.org/pdf/2602.00730](https://arxiv.org/pdf/2602.00730)  
42. Dual-branch Graph Domain Adaptation for Cross-scenario Multi-modal Emotion Recognition, [https://arxiv.org/html/2603.26840v1](https://arxiv.org/html/2603.26840v1)  
43. M4Rec: Multi-Modal Knowledge Graph Modeling of Multi-Dimensional User Preferences for Next-POI Recommendation \- IEEE Xplore, [https://ieeexplore.ieee.org/iel8/69/11503382/11455341.pdf](https://ieeexplore.ieee.org/iel8/69/11503382/11455341.pdf)  
44. Multi‐Modal Knowledge Graph Hybrid Attention Network for Recommendation, [https://www.researchgate.net/publication/403977553\_Multi-Modal\_Knowledge\_Graph\_Hybrid\_Attention\_Network\_for\_Recommendation](https://www.researchgate.net/publication/403977553_Multi-Modal_Knowledge_Graph_Hybrid_Attention_Network_for_Recommendation)  
45. Knowledge graph-based personalized multimodal recommendation fusion framework \- arXiv, [https://arxiv.org/pdf/2509.02943](https://arxiv.org/pdf/2509.02943)  
46. Multimodal Relation Extraction Method Based on Expert Framework and Hierarchical Visual Features \- ResearchGate, [https://www.researchgate.net/publication/400043520\_Multimodal\_Relation\_Extraction\_Method\_Based\_on\_Expert\_Framework\_and\_Hierarchical\_Visual\_Features](https://www.researchgate.net/publication/400043520_Multimodal_Relation_Extraction_Method_Based_on_Expert_Framework_and_Hierarchical_Visual_Features)  
47. Multimodal Deep-Context Knowledge Extraction | PDF | Information \- Scribd, [https://www.scribd.com/document/901937615/MDCKE-Multimodal-Deep-context-Knowledge-Extractor-That-Integrates](https://www.scribd.com/document/901937615/MDCKE-Multimodal-Deep-context-Knowledge-Extractor-That-Integrates)  
48. Optimizing data extraction from materials science literature: a study of tools using large language models \- Digital Discovery (RSC Publishing) DOI:10.1039/D5DD00482A, [https://pubs.rsc.org/en/content/articlehtml/2026/dd/d5dd00482a](https://pubs.rsc.org/en/content/articlehtml/2026/dd/d5dd00482a)  
49. Large-Scale Language Model Assisted Construction of Multi-Source Heterogeneous Knowledge Graphs for Marine Renewable Energy \- SCIEPublish, [https://www.sciepublish.com/article/pii/834](https://www.sciepublish.com/article/pii/834)  
50. Technological advancements in multi-modal knowledge graphs for engineering management \- Emerald Insight, [https://www.emerald.com/ecam/article/doi/10.1108/ECAM-02-2024-0252/1258908/Technological-advancements-in-multi-modal](https://www.emerald.com/ecam/article/doi/10.1108/ECAM-02-2024-0252/1258908/Technological-advancements-in-multi-modal)  
51. Event Extraction in Large Language Model: A Holistic Survey of Method, Modality, and Future \- arXiv, [https://arxiv.org/html/2512.19537v1](https://arxiv.org/html/2512.19537v1)  
52. The Best Open Source LLMs For Knowledge Graph Construction In 2026 \- SiliconFlow, [https://www.siliconflow.com/articles/en/best-open-source-LLM-for-Knowledge-Graph-Construction](https://www.siliconflow.com/articles/en/best-open-source-LLM-for-Knowledge-Graph-Construction)  
53. LLM Knowledge Graph Builder — First Release of 2025 \- Neo4j, [https://neo4j.com/blog/developer/llm-knowledge-graph-builder-release/](https://neo4j.com/blog/developer/llm-knowledge-graph-builder-release/)  
54. Knowledge Graph and LLM \- Xin Cheng, [https://billtcheng2013.medium.com/knowledge-graph-and-llm-19822c0c0477](https://billtcheng2013.medium.com/knowledge-graph-and-llm-19822c0c0477)  
55. pyg-team/pytorch\_geometric: Graph Neural Network Library for PyTorch \- GitHub, [https://github.com/pyg-team/pytorch\_geometric](https://github.com/pyg-team/pytorch_geometric)  
56. PyG 2.0: Scalable Learning on Real World Graphs \- arXiv, [https://arxiv.org/html/2507.16991v2](https://arxiv.org/html/2507.16991v2)  
57. Huajun Chen \- The Knowledge Graph Conference, [https://www.knowledgegraph.tech/blog/speakers/huajun-chen/](https://www.knowledgegraph.tech/blog/speakers/huajun-chen/)  
58. heathersherry/Knowledge-Graph-Tutorials-and-Papers \- GitHub, [https://github.com/heathersherry/knowledge-Graph-Tutorials-and-Papers](https://github.com/heathersherry/knowledge-Graph-Tutorials-and-Papers)  
59. Construction of a Linked Data Set of COVID-19 Knowledge Graphs: Development and Applications \- JMIR Medical Informatics, [https://medinform.jmir.org/2022/5/e37215/](https://medinform.jmir.org/2022/5/e37215/)  
60. OpenKG Chain: A Blockchain Infrastructure for Open Knowledge Graphs \- MIT Press Direct, [https://direct.mit.edu/dint/article/3/2/205/101024/OpenKG-Chain-A-Blockchain-Infrastructure-for-Open](https://direct.mit.edu/dint/article/3/2/205/101024/OpenKG-Chain-A-Blockchain-Infrastructure-for-Open)  
61. Knowledge Graph and LLMs.md \- GitHub, [https://github.com/heathersherry/Knowledge-Graph-Tutorials-and-Papers/blob/master/topics/Knowledge%20Graph%20and%20LLMs.md](https://github.com/heathersherry/Knowledge-Graph-Tutorials-and-Papers/blob/master/topics/Knowledge%20Graph%20and%20LLMs.md)