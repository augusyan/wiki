---
title: 论文图表设计原则
created: 2026-05-13
updated: 2026-05-13
type: concept
tags: [drawing, figure-design, paper-writing, visualization]
sources: [raw/articles/paper-draft.md]
confidence: medium
---

# 论文图表设计原则

## 配色方案

### Morandi 静默调色板（BridgeKG 论文使用）
- `mist-stone`（默认）：`#F3EEE8`, `#D8D1C7`, `#8A9199`
- `sage-clay`（方法 vs 基线对比）：`#E7E1D6`, `#B7A99A`, `#7F8F84`
- `dust-rose`（次要对比）：`#F2E9E6`, `#D8C3BC`, `#B88C8C`

### 通用规则
- 白色/近白色背景
- 禁用霓虹色、彩虹/jet 色图
- 禁用重阴影、光泽渐变、粗黑边框
- 去除顶部和右侧坐标轴脊线
- 图例最小化，优先直接标注
- 主要方法视觉突出

## 图表类型选择

| 数据类型 | 推荐图表 |
|---------|---------|
| 步骤/轮次趋势 | 折线图 |
| 类别终点对比 | 柱状图（含零点基线） |
| 不确定性比较 | 点-区间/散点图 |
| 分布问题 | 箱线/小提琴/直方图 |
| 矩阵结构 | 热力图 |

## 自查清单

- [ ] 主信息是否在几秒内就能理解？
- [ ] 标签、单位、基线是否明确？
- [ ] 图例是否太大或遮挡数据？
- [ ] 缩小后文字是否仍可读？
- [ ] 主要方法是否视觉上最突出？
- [ ] 灰度图是否仍能区分？
- [ ] 是否有无意义的装饰元素？

## 待积累

- [ ] 各顶会 Best Paper 的图表风格分析
- [ ] drawio 架构图模板库
- [ ] Manim 动画生成脚本模板

See also: [[concepts/writing/paper-structures]]
