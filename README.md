# Personal Research Wiki

> 个人科研知识库 — LLM 驱动、Git 同步、Obsidian 可视化

## 这是什么

一个由 **Hermes Agent + LLM Wiki** 驱动的结构化科研知识库。不仅存储论文笔记和项目文档，更重要的是 LLM 会主动维护页面间的交叉引用、检测矛盾、更新索引，形成**数据飞轮**效应——每摄入一篇新内容，整个知识库都会变得更丰富。

## 快速开始

```bash
# 1. 克隆
git clone git@github.com:augusyan/wiki.git

# 2. Obsidian 打开
# Obsidian → Open folder as vault → 选择 wiki 目录

# 3. 安装 Obsidian 插件（推荐）
# - Diagrams (Draw.io 集成，直接在笔记里画图)
# - Dataview (查询 frontmatter 元数据)
```

## 目录结构

```
wiki/
├── README.md               # 本文件
├── SCHEMA.md               # 领域约定、标签分类法、页面规范
├── index.md                # 内容目录（按类型组织，每个页面一行摘要）
├── log.md                  # 操作日志（追加式，按日期记录所有动作）
│
├── raw/                    # 第一层：不可变原始材料（LLM 只读）
│   ├── papers/             #   论文原文 + 精读笔记
│   │   ├── acl2025/        #     45 篇 ACL 2025 IE 论文笔记
│   │   ├── emnlp2025/      #     18 篇 EMNLP 2025 IE 论文笔记
│   │   └── nsfc-disinfo/   #     39 篇虚假信息防御论文 PDF
│   ├── projects/           #   项目原始文档
│   └── articles/           #   任务简报、实验计划等
│
├── entities/               # 第二层：实体页面（LLM 维护）
│   ├── okg-llm.md          #   OKG-LLM 方法详解
│   ├── bridgekg-llm.md     #   BridgeKG-LLM 方法与实验结果
│   ├── bskg.md             #   桥梁结构知识图谱
│   ├── z24-bridge.md       #   Z24 基准数据集
│   ├── timellm.md          #   TimeLLM 方法
│   ├── dlinear.md          #   DLinear 基线
│   └── projects/           #   研究项目总览
│
├── concepts/               # 第二层：概念与技巧页面（LLM 维护）
│   ├── kg-llm-time-series-forecasting.md
│   ├── structural-health-monitoring.md
│   ├── information-extraction-landscape.md
│   ├── paper-collection-acl-emnlp-2025.md
│   ├── writing/            #   论文写作技巧
│   └── drawing/            #   图表设计原则
│
├── comparisons/            # 第二层：对比分析
│   ├── z24-experiment-results.md
│   └── okg-vs-bridgekg.md
│
└── templates/              # 可复用模板
    └── paper-outline.md    #   论文大纲模板
```

## 使用方式

### 通过 Hermes Agent

```bash
# 加载 LLM Wiki skill
hermes -s llm-wiki

# 然后对话式操作：
# "摄入这篇论文" → LLM 自动创建实体页、更新索引、建立交叉引用
# "Z24 实验哪个模型最好？" → LLM 检索 Wiki 并综合答案
# "检查 Wiki 健康状态" → LLM 检测孤立页、死链、矛盾、过期内容
```

### 通过 Obsidian

- **Graph View** (`Ctrl+G`)：可视化知识网络，发现隐藏联系
- **搜索** (`Ctrl+Shift+F`)：全文检索所有笔记和论文
- **Dataview**：查询所有 `tags: [ner]` 的论文，或 `venue: ACL` 的笔记
- **Draw.io**：新建 `.drawio.svg` 文件，在笔记里画架构图

### 通过 Git

```bash
# 服务器端（LLM 修改后）
cd /data/yantianwei/wiki
git add -A && git commit -m "update" && git push

# 笔记本端（同步到本地 Obsidian）
cd ~/wiki && git pull
```

## 核心工作流

```
新论文/资料 → 放入 raw/ → 告诉 LLM "摄入"
     ↓
LLM 自动处理：
  1. 读取原文 → 提取关键信息
  2. 检查已有页面 → 避免重复
  3. 创建/更新实体页 + 概念页
  4. 建立 [[交叉引用]]
  5. 更新 index.md + log.md
     ↓
Obsidian 端 git pull → Graph View 显示新节点和连线
     ↓
积累足够 → 蒸馏为 Agent Skill → 下次自动复用
```

## 当前内容概览

| 类别 | 数量 | 说明 |
|------|------|------|
| 论文笔记 | 63 篇 | ACL/EMNLP 2025，信息抽取方向 |
| NSFC 论文 | 39 篇 | 虚假信息防御 |
| 研究项目 | 2 个 | BridgeKG-LLM + NSFC 虚假信息防御 |
| Wiki 页面 | 18 个 | 实体页、概念页、对比页、模板 |
| 标签分类 | 30+ | 覆盖方法、领域、任务、写作 |

## 设计原则

1. **原始材料不可变** — `raw/` 内容永远不改，事实源头
2. **Wiki 页面 LLM 拥有** — 创建、更新、交叉引用全由 LLM 维护
3. **SCHEMA 人机共演** — 标签分类法和页面规范由人和 LLM 共同演化
4. **每次摄入都增强整体** — 新论文不仅添加新页面，还会更新相关已有页面
5. **Git 即版本历史** — 每次修改都可追溯，免费获得备份

## 相关资源

- [LLM Wiki 原论文](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) — Andrej Karpathy
- [Hermes Agent](https://github.com/NousResearch/hermes-agent) — AI Agent 框架
- [Obsidian](https://obsidian.md/) — 知识图谱可视化
