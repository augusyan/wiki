---
source_path: /data/yantianwei/notes/ACL_2025_GEMS.md
ingested: 2026-05-13
sha256: a29ad0389614f4c9
---
# ACL_2025_GEMS



# 基因本体论（Gene Ontology）入门指南

## 文献信息

- **标题**: Primer on the Gene Ontology（基因本体论入门指南）
- **作者**: Pascale Gaudet, Nives Škunca, James C. Hu, Christophe Dessimoz
- **单位**:
  - SIB Swiss Institute of Bioinformatics, 瑞士日内瓦
  - ETH Zurich, 瑞士苏黎世
  - University College London, 英国伦敦
  - Texas A&M University, 美国德克萨斯州
  - University of Lausanne, 瑞士洛桑
- **发表时间**: 2016年1月
- **类型**: 书籍章节（Book Chapter）

## 一句话总结

本文是基因本体论（Gene Ontology, GO）的入门指南，介绍了GO的结构、三大本体分支、注释文件的格式与证据代码，以及GO在基因功能注释和功能分析中的应用。

**代码链接**: 无

---

## 背景

### 基因本体论的起源

基因本体论项目的核心动机源于一个重要观察：**相似基因在不同生物中往往具有保守的功能**。这一认识可以追溯到Jacques Monod的名言："任何在大肠杆菌中被证实为真的规律，在大象身上也必然成立"（"anything found to be true of E. coli must also be true of elephants"）。

在GO出现之前，不同数据库使用各自独立的词汇来描述基因功能，这使得跨物种比较基因功能变得极为困难。GO项目旨在建立一个严格的共享词汇体系，以描述不同生物体中基因的功能，从而实现：
- 整合来自不同数据库的信息
- 推断新发现基因的功能
- 深入了解生物子系统的保守性与分化

### GO的基本概念

Gene Ontology是一个受控词汇表，以结构化方式描述生物学。它包含三个独立的本体（ontologies）：

1. **分子功能（Molecular Function, MF）**: 基因产物执行的分子级生物学活动
2. **生物过程（Biological Process, BP）**: 由多个分子功能有序组合完成的生物学过程
3. **细胞组分（Cellular Component, CC）**: 基因产物在细胞中的位置

截至2015年10月，GO完整本体规范包含：
- 43,835个术语
- 73,776个显式编码的 *is_a* 关系
- 7,436个显式编码的 *part_of* 关系
- 8,263个显式编码的 *regulates* 关系

---

## 动机

### 为什么需要统一的基因功能描述框架？

1. **标准化词汇需求**: 不同数据库使用不同术语描述相同功能，导致数据整合困难
2. **跨物种比较**: 便于比较不同物种中直系同源基因（orthologs）的功能
3. **功能推断**: 可基于已注释基因推断新发现基因的功能
4. **高通量数据分析**: 用于分析基因表达实验结果，进行功能富集分析

### GO的核心价值

- **非冗余性**: 三个本体共享标识符空间和明确指定的语法
- **层次结构**: 术语之间通过关系链接形成有向无环图（DAG）
- **可扩展性**: 持续更新以反映最新科学知识

---

## 相关工作

### GO Consortium成员机构

- UniProt
- Mouse Genome Informatics (MGI)
- Saccharomyces Genome Database (SGD)
- WormBase
- FlyBase
- dictyBase
- TAIR (The Arabidopsis Information Resource)
- EcoCyc
- Functional Gene Annotation group at UCL

### 现有工具与资源

- **AmiGO浏览器**: 在线GO术语和注释数据访问工具
- **GO-Slim**: GO的手动精选子集，包含通用高层术语
- **QuickGO**: EBI提供的GO术语浏览器
- 多种GO分析工具（详见书中其他章节）

---

## 方法

### GO本体结构

GO是一个有向图结构：
- **节点**: GO术语
- **边**: 关系（*is_a*, *part_of*, *has_part*, *regulates*）

关系类型说明：
- *is_a*: 表示"是一种"的关系
- *part_of*: 表示"是...的一部分"
- *regulates* / *negatively_regulates* / *positively_regulates*: 调控关系

### GO注释文件格式

#### GAF（Gene Association File）2.1格式

GAF文件包含17个字段，每行代表一个注释：

| 字段 | 描述 | 示例 |
|------|------|------|
| 1 | 数据库 | UniProtKB |
| 2 | 数据库登录号 | P00519 |
| 3 | 数据库对象符号 | ABL1 |
| 4 | 限定符 | NOT, contributes_to, colocalizes_with |
| 5 | GO术语 | GO:0004713 |
| 6 | 参考 | PMID:12345678 |
| 7 | 证据代码 | EXP, IDA, IPI, IMP, IGI, IEP, ISS, IEA等 |
| 8 | With/From | 支持信息 |
| 9 | 本体方面 | MF, BP, CC |
| 10 | 数据库对象名称 | Proto-oncogene tyrosine-protein kinase ABL1 |
| 11 | 数据库对象同义词 | ABL, c-ABL |
| 12 | 数据库对象类型 | protein, gene, RNA等 |
| 13 | 生物分类ID | taxon:9606 |
| 14 | 日期 | 20151106 |
| 15 | 分配者 | UniProtKB |
| 16 | 注释扩展 | part_of(CL:0000084 T cell) |
| 17 | 基因产物形式ID | P00519-1 |

### 证据代码体系

GO证据代码分为三大类：

#### 1. 实验证据代码（Experimental Evidence）

- **EXP**: Inferred from Experiment（实验推断）
- **IDA**: Inferred from Direct Assay（直接测定推断）
- **IPI**: Inferred from Physical Interaction（物理相互作用推断）
- **IMP**: Inferred from Mutant Phenotype（突变表型推断）
- **IGI**: Inferred from Genetic Interaction（遗传相互作用推断）
- **IEP**: Inferred from Expression Pattern（表达模式推断）

#### 2. 手动注释的非实验证据代码

- **ISS**: Inferred from Sequence or Structural Similarity（序列或结构相似性推断）
  - **ISA**: Inferred from Sequence Alignment（序列比对推断）
  - **ISO**: Inferred from Sequence Orthology（序列直系同源推断）
  - **ISM**: Inferred from Sequence Model（序列模型推断）
- **IGC**: Inferred from Genomic Context（基因组上下文推断）
- **IBA/IBD**: Inferred from Biological aspect of Ancestor/Descendant（祖先/后代生物学方面推断）
- **IKR**: Inferred from Key Residues（关键残基推断）
- **IRD**: Inferred from Rapid Divergence（快速分歧推断）
- **RCA**: Inferred from Reviewed Computational Analysis（审查计算分析推断）
- **TAS**: Traceable Author Statement（可追溯作者声明）
- **NAS**: Non-traceable Author Statement（不可追溯作者声明）
- **IC**: Inferred by Curator（ curator推断）
- **ND**: No biological Data available（无生物数据可用）

#### 3. 自动分配证据代码

- **IEA**: Inferred from Electronic Annotation（电子注释推断）

### 限定符（Qualifiers）

- **NOT**: 表示该功能不存在
- **contributes_to**: 表示对分子功能的贡献
- **colocalizes_with**: 表示共定位

---

## 实验设计

本文为入门指南性质，主要采用综述和说明性方式：

1. **术语结构说明**: 通过图示展示GO的层次结构
2. **文件格式解析**: 详细说明GAF文件的17个字段
3. **证据代码分类**: 系统介绍21种证据代码的用途
4. **实例分析**: 使用具体示例（如P00519/ABL1蛋白）说明注释格式

---

## 数据集

### GO本体数据

- 43,835个GO术语（截至2015年10月）
- 关系数据：73,776个 *is_a* 关系，7,436个 *part_of* 关系，8,263个调控关系

### 注释数据来源

- UniProtKB数据库
- Mouse Genome Informatics
- Saccharomyces Genome Database (SGD)
- WormBase
- FlyBase
- dictyBase
- TAIR
- EcoCyc
- UCL功能注释组

### GO-Slim子集

- Generic GO-Slim（GO Consortium开发）
- Chembl Drug Target Slim（特定领域子集）

---

## 结果与分析

### 关键发现

1. **注释唯一性**: 同一基因可使用不同证据代码注释到同一GO术语
2. **注释扩展功能**: 字段16支持将多个概念（如蛋白质、细胞类型）组合在同一注释中
3. **版本管理**: GO文件每日更新，版本号为文件获取日期

### 重要注意事项

1. **偏见问题**: 
   - 实验证据注释通常被认为质量更高
   - 自动注释（IEA）倾向于注释到高层术语，信息量较低
   - 某些基因在数据库中有多条记录，可能导致统计分析偏差

2. **UniProt数据冗余**:
   - 人类蛋白质UniProt条目超过70,000个
   - 实际人类蛋白质编码基因约20,000个
   - 建议使用"gene-centric"参考蛋白质组列表进行基因中心化分析

3. **术语废弃处理**:
   - 术语从不删除，改为"obsolete"状态
   - 废弃术语的注释可能需要手动审查

---

## 未来可能改进方向

1. **注释质量提升**:
   - 建立更严格的质量控制机制
   - 开发自动化验证工具

2. **证据代码优化**:
   - 进一步完善ECO（Evidence and Conclusion Ontology）与GO的集成
   - 开发更细粒度的证据分类

3. **跨本体整合**:
   - 增强GO与其他生物本体（如Cell Ontology, Protein Ontology）的互操作性
   - 改进注释扩展功能的应用

4. **社区参与机制**:
   - 扩展社区注释渠道
   - 建立更完善的反馈和改进机制

5. **计算预测方法改进**:
   - 提高电子注释的准确性
   - 开发更可靠的基于机器学习的功能预测方法

---

## 总结

本文作为Gene Ontology的入门指南，系统性地介绍了GO的基本概念、结构、注释格式和证据代码体系。对于生物信息学研究人员而言，理解GO的层次结构和注释规范是进行功能基因组学分析的基础。GO作为最大的基因功能注释资源，已成为生物医学研究中不可或缺的工具。