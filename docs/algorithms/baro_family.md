# 统计偏差类算法（BARO / NSigma / MMBARO / MMNSigma）

## 代表代码入口

- `RCAEval/e2e/baro.py`
- `RCAEval/e2e/__init__.py` 中 `nsigma`

## 核心实现原理

1. 切分窗口：按 `inject_time` 将数据拆分为 normal/anomalous。
2. 预处理：对两段分别调用 `preprocess(...)`。
3. 列对齐：取交集列，确保逐列可比。
4. 异常打分：对每列用 normal 拟合 scaler，再变换 anomalous，取最大偏移作为分数。
5. 排序输出：按分数降序得到 `ranks`。

## 分数定义

对第 $j$ 列特征：

$$
s_j = \max_t \; z_{j,t}
$$

其中 $z_{j,t}$ 为异常段样本经 normal 段拟合 scaler 后的标准化值。

## BARO vs NSigma

1. `baro/mmbaro`：`RobustScaler`，中心与尺度基于中位数和 IQR，抗离群值。
2. `nsigma/mmnsigma`：`StandardScaler`，中心与尺度基于均值和标准差。

## 多源实现说明（mm 系列）

当前实现是“多源统一排序”而非显式源级权重：

1. metrics/logts/traces 特征分别算分。
2. 合并到同一列表后直接排序。

因此文档中的“加权融合”建议理解为“多源融合打分”。如需严格源权重，应新增显式聚合层。

## 参数与注意事项

1. `dk_select_useful` 会影响参与打分的列集合。
2. `dataset` 会影响 traces 子分支是否启用。
3. 使用 `max(z)` 偏向上升型异常；若要双侧异常，可扩展为 `max(abs(z))`。

## 适用场景

1. 冷启动或链路缺失场景。
2. 需要高吞吐、低计算开销的快速初筛。
