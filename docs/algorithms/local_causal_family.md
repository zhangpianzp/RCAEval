# 局部因果发现算法族（RCD / MMRCD / EasyRCA / CIRCA）

## 代表代码入口

- `RCAEval/e2e/rcd.py`
- `RCAEval/e2e/mmrcd.py`
- `RCAEval/e2e/easyrca.py`
- `RCAEval/e2e/circa.py`

## 核心实现思想

这类方法不是先求全图，而是围绕故障标签（如 F-node）做局部发现：

1. 构造 normal/anomalous 标签。
2. 以局部独立性检验（多为 PC skeleton 变体）寻找直接相关邻域。
3. 逐步扩展或排序候选根因。

## 为什么更可扩展

1. 避免全图搜索带来的组合爆炸。
2. 只聚焦故障相关子空间，计算更集中。

## 关键参数

1. `alpha`：独立性检验阈值。
2. 离散化参数（如 `KBinsDiscretizer` 的 bins）。
3. 迭代步进策略（如从小到大扫描 alpha）。

## 适用场景

1. 指标维度高、需要控制计算开销。
2. 需要保留一定因果解释但不能承担全图成本。

## 局限

1. 局部假设不成立时可能漏报间接根因。
2. 对离散化和检验阈值较敏感。
