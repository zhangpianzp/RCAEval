# 概率与外部库算法族（CausalAI / PDiagnose / E-Diagnosis）

## 代表代码入口

- `RCAEval/e2e/causalai.py`
- `RCAEval/e2e/pdiagnose.py`
- `RCAEval/e2e/__init__.py` 中 `e_diagnosis`

## CausalAI 实现思路

1. 将 normal/anomalous 数据整理为 CausalAI 所需输入格式。
2. 调用 `RootCauseDetector` 进行因果与根因推断。
3. 返回库输出的根因集合/排序。

## PDiagnose / E-Diagnosis 实现思路

1. 对 normal/anomalous 窗口做对齐预处理。
2. 使用 PyRCA 的 `EpsilonDiagnosis` 类训练与推断。
3. 按 root cause score 排序输出。

## 特点

1. 开发与维护成本低，复用成熟实现。
2. 参数数量相对可控，便于快速对比基线。

## 局限

1. 对外部库版本兼容较敏感。
2. 深层细节由外部实现决定，可定制性相对受限。
