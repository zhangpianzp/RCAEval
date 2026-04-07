# RCAEval 算法文档索引

本目录按“算法家族 + 具体方法”拆分文档，便于快速跳转与维护。

## 按算法家族

1. 统计偏差类：`baro_family.md`
2. 图构建 + PageRank：`graph_pagerank_family.md`
3. 图构建 + RandomWalk：`graph_randomwalk_family.md`
4. 局部因果发现：`local_causal_family.md`
5. Trace 专用：`trace_family.md`
6. 深度学习类：`deep_family.md`
7. 概率/外部库类：`probabilistic_family.md`

## 按具体方法

### 统计偏差类
- `baro`（`RCAEval/e2e/baro.py`）
- `nsigma`（`RCAEval/e2e/__init__.py` 中实现）
- `mmbaro/mmnsigma`（`RCAEval/e2e/baro.py`）

### 图构建 + 排序类
- `pc_pagerank`、`cmlp_pagerank`、`ntlr_pagerank`（`RCAEval/e2e/pc_pagerank.py`）
- `fci_pagerank`、`ges_pagerank`、`granger_pagerank`、`lingam_pagerank`（对应 `RCAEval/e2e/*_pagerank.py`）
- `pc_randomwalk`、`fci_randomwalk`、`lingam_randomwalk`、`granger_randomwalk`、`ntlr_randomwalk`（`RCAEval/e2e/pc_randomwalk.py`）

### 局部因果发现类
- `rcd`（`RCAEval/e2e/rcd.py`）
- `mmrcd`（`RCAEval/e2e/mmrcd.py`）
- `easyrca`（`RCAEval/e2e/easyrca.py`）
- `circa`（`RCAEval/e2e/circa.py`）

### Trace 专用
- `tracerca`（`RCAEval/e2e/tracerca.py`）
- `microrank`（`RCAEval/e2e/microrank.py`）

### 深度学习类
- `mscred`（`RCAEval/e2e/mscred.py`）
- `causalrca`（`RCAEval/e2e/causalrca.py`）

### 概率/外部库类
- `causalai`（`RCAEval/e2e/causalai.py`）
- `pdiagnose/e_diagnosis`（`RCAEval/e2e/pdiagnose.py`, `RCAEval/e2e/__init__.py`）
