# Trace 专用算法族（TraceRCA / MicroRank）

## 代表代码入口

- `RCAEval/e2e/tracerca.py`
- `RCAEval/e2e/microrank.py`

## TraceRCA 实现原理

### 1) 数据建模与窗口切分

在 `RCAEval/e2e/tracerca.py` 中，TraceRCA 先将 span 级字段统一为 operation：

1. 若 `methodName` 缺失，则用 `operationName` 回填。
2. 构造 `operation = serviceName + "_" + methodName`。
3. 用 `startTime + duration < inject_time` 划分正常窗口 `normal_df`。
4. 用 `startTime + duration >= inject_time` 划分异常窗口 `anomal_df`。

该划分对应“故障注入前后”思想：注入前用于学习基线，注入后用于识别异常行为。

### 2) 基线 SLO 学习（正常窗口）

对每个 operation，基于正常窗口时延学习统计基线：

1. 均值：$\mu_{op} = \mathrm{mean}(duration_{op})$
2. 标准差：$\sigma_{op} = \mathrm{std}(duration_{op})$

实现上用 `get_operation_slo(...)` 计算，并以毫秒量纲保存（代码中使用 `/1000` 做单位换算）。

### 3) span 异常判定（异常窗口）

对异常窗口中每条 span，按 operation 对应基线做 3-sigma 判定：

$$
abnormal = [duration \ge \mu_{op} + 3\sigma_{op}]
$$

其中 `abnormal` 是布尔值，表示该 span 是否显著超时。

### 4) 可疑 operation 挖掘

对每个 operation $A$，计算两个指标：

1. support（异常覆盖率）

$$
s_A = \frac{|\{abnormal\ spans\ of\ A\}|}{|\{all\ abnormal\ spans\}|}
$$

2. confidence（自身异常率）

$$
c_A = \frac{|\{abnormal\ spans\ of\ A\}|}{|\{all\ spans\ of\ A\ in\ anomalous\ window\}|}
$$

前者衡量“该 operation 对整体异常的贡献占比”，后者衡量“该 operation 本身是否稳定异常”。

### 5) JI 融合与排序

TraceRCA 使用调和融合得到最终可疑分数（代码变量 `ji_dict`）：

$$
JI_A = \frac{2 s_A c_A}{s_A + c_A}
$$

最后按 `JI` 从高到低排序得到 `ranks`。

这种融合会同时偏好“覆盖面大”且“自身异常率高”的 operation，避免仅由单一指标主导。

### 6) 实现细节说明

1. 排序后会跳过 `NaN` 分数（例如分母为 0 引起的无效项）。
2. 返回结果为 operation 名称列表，不直接返回分数。
3. 结果粒度是 operation 级，而不是 trace 级或 service 级。

## MicroRank 实现原理

1. 从 traces 构建 operation-request 二部关系。
2. 建立多种转移矩阵（operation->operation, operation->request, request->operation）。
3. 进行迭代排名，结合传播项与先验项得到 operation 权重。

## 适用场景

1. 链路数据完整、服务调用关系复杂。
2. 需要 operation 级定位而非仅服务指标级定位。

## 局限

1. trace 缺失、采样不稳定会显著影响效果。
2. 字段标准化质量（serviceName/methodName）直接影响建模质量。
