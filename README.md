# AcadSearch — 实验数据 (Experimental Data)

本仓库包含 AcadSearch 论文所用的核心实验数据（与论文表格直接对应）。

## 数据文件说明

| 文件 | 大小 | 说明 |
|------|------|------|
| `benchmark_data.json` | ~102 KB | 基准测试集定义：60 个查询（easy 22 / medium 20 / hard 18），覆盖 5 个领域（hallucination / diffusion / rl_code / vlm_alignment / gnn_ssl），共 111 篇标注论文。 |
| `full_experiment_results.json` | ~426 KB | **主实验结果**：60 个查询 × 2 种策略（`agent` = LLM Agent / `agent_qa` = QA-Agent, th=0.30）的完整检索结果，含每轮质量感知、judge 五维打分（relevance / coverage / recency / authority / diversity）、token 消耗、早停判断等。论文主表、难度分层、域分层、token 节省均源自此文件。 |
| `ablation_results.json` | ~27 KB | 消融实验结果：不同阈值（θ∈{0.05,0.15,0.30}）下 QA-Agent 各模块（S1/M2/M3/H1 等）的 token 节省与质量变化。 |

## 数据字段（full_experiment_results.json）

每个查询记录包含：

- `query_id` / `query` / `difficulty` / `domain`：查询标识、内容、难度（easy/medium/hard）、领域
- `strategy` / `strategy_name`：策略（agent = LLM Agent，agent_qa = QA-Agent）
- `num_papers`：检索到的论文数
- `cost`：token 消耗（total_tokens / api_calls / rounds / elapsed），QA-Agent 含 `quality_history`（每轮质量感知）
- `judge`：LLM 裁判五维打分（relevance / coverage / recency / authority / diversity / overall），含分数与理由
- `scoring`：`continuous`

## 复现

数据由 `experiments/` 目录下的脚本产生：
- 主实验：`run_full_experiment.py`（并行跑 benchmark_data.json 的 60 查询）
- 消融：`ablation_experiment.py`
- 统计检验：`statistical_test.py` / `final_statistical_test.py`
- 效应量/功效分析：`effect_size_analysis.py` / `power_analysis.py`

主代码位于 [zxcvbnm101044/acadsearch](https://github.com/zxcvbnm101044/acadsearch)。

> 注：本仓库数据仅供论文复现与学术使用。文件名与字段结构以最新的 `full_experiment_results.json`（2026-08-04）为准。
