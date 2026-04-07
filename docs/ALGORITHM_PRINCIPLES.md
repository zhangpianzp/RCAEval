# RCAEval 算法原理总览

本文档改为“总览 + 导航页”。

详细算法说明已按方法拆分到目录 `docs/algorithms/`，方便按算法快速检索与维护。

## 快速索引

- 统一索引页：`docs/algorithms/INDEX.md`
- 统计偏差类：`docs/algorithms/baro_family.md`
- 图构建 + PageRank：`docs/algorithms/graph_pagerank_family.md`
- 图构建 + RandomWalk：`docs/algorithms/graph_randomwalk_family.md`
- 局部因果发现：`docs/algorithms/local_causal_family.md`
- Trace 专用：`docs/algorithms/trace_family.md`
- 深度学习类：`docs/algorithms/deep_family.md`
- 概率/外部库类：`docs/algorithms/probabilistic_family.md`

## 统一问题定义

RCAEval 大多数方法都遵循相同任务形式：

1. 输入故障前后窗口数据（常见含 `time` 列）。
2. 切分 normal/anomalous 或构造等价标签。
3. 计算候选根因分数并排序。
4. 返回 `ranks`（可选返回 `node_names` 和 `adj`）。

## 通用实现流水线

1. 预处理（去冗余、列对齐、缺失处理）。
2. 主体算法（统计偏差 / 图推断 / 深度模型 / trace 挖掘）。
3. 排序头（直接排序、PageRank、RandomWalk 等）。

## PC 方法做了什么

PC（Peter-Clark）是约束型因果发现方法。在 RCAEval 中，它的角色主要是“先构图，再排序”：

1. 把每个指标视为一个节点，初始假设节点间可能存在依赖关系。
2. 通过条件独立性检验，逐步删除不成立的边，得到骨架图。
3. 基于分离集和方向规则对边定向，形成有向因果图（可能包含部分未定向关系）。
4. 将图结构转成邻接矩阵，交给下游排序头（PageRank 或 RandomWalk）输出根因排名。

在本仓库中，核心入口是 `RCAEval/graph_construction/pc.py` 的 `pc_default(...)`：

1. 将 DataFrame 转为数值矩阵并调用 `causallearn` 的 `pc(...)`。
2. 可选注入 background knowledge（例如禁止某些明显不合理的方向）。
3. 返回 `cg.G.graph` 作为后续排序的图输入。

典型调用链：

1. `RCAEval/e2e/pc_pagerank.py`：PC 构图 + PageRank 排序。
2. `RCAEval/e2e/pc_randomwalk.py`：PC 构图 + RandomWalk 排序。

简化理解：PC 负责“谁影响谁”的结构发现，排序算法负责“谁最像根因”的优先级计算。

## 如何阅读分文档

每个算法文档统一包含以下结构：

1. 代表方法与代码入口
2. 输入与前置条件
3. 逐步实现原理
4. 分数/权重含义
5. 复杂度与局限
6. 适用场景与调参建议

## 相关文档

- 扩展方法：`docs/EXTENDING.md`
- 环境依赖：`docs/SETUP.md`
- 使用与复现实验：`README.md`
