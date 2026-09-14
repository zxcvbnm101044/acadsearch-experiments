# AcadSearch — 实验数据 (Experimental Data)

本仓库包含 AcadSearch 论文所用的核心实验数据（与论文表格直接对应）。

## 数据文件说明

| 文件 | 大小 | 说明 |
|------|------|------|
| `benchmark_data.json` | ~102 KB | 基准测试集定义：60 个查询（easy 22 / medium 20 / hard 18），覆盖 5 个领域，共 111 篇真实论文。 |
| `full_experiment_results.json` | ~485 KB | **主实验结果**：60 个查询 × 2 种策略（`agent` = LLM Agent / `agent_qa` = QA-Agent，θ_q = 0.50）的完整检索结果，含每轮质量感知（`quality_history`）、judge 五维打分（relevance / coverage / recency / authority / diversity）、token 消耗、早停判断等。论文主表（Table 2）、触发分层（Table 3）、难度分层（Table 4）与本仓库数据一一对应。 |
| `ablation_results.json` | ~27 KB | 消融实验结果：不同阈值（θ_q ∈ {0.05, 0.10, 0.15, 0.20, 0.30}）下 QA-Agent 各模块（S1/M2/M3/H1 等）的 token 节省与质量变化。 |

## 基准构成（与论文 4.1 节一致）

- **5 个领域，111 篇真实论文**：LLM hallucination (22) / diffusion models (23) / RL for code generation (24) / vision-language alignment (21) / GNN self-supervised learning (21)。
- **元数据**：每篇论文含 title / authors / abstract / year / citation count / venue / keywords，均来自 NeurIPS、ICML、ICLR、CVPR、ACL、EMNLP、AAAI 等顶会真实发表论文。
- **60 个测试查询**：22 easy（综述类）/ 20 medium（方法对比）/ 18 hard（跨域或小众主题），**全部为中文**，反映中文科研用户的真实使用场景。
- 检索由 `LocalSearcher` 通过 TF-IDF + 精确匹配模拟，同一语料下所有策略条件一致，每次最多返回 20 篇、按相关度排序。

## 核心结果（与论文 Table 2 / 3 对应）

- 全 60 查询：QA-Agent 质量与标准 Agent 无统计显著差异（overall 2.998 vs. 3.008，p = 0.64），聚合 token 节省 **14.1%**（p = 0.11，不显著）。
- 早停触发子集：32/60（**53.3%**），该子集 token 节省 **46.1%**（275,479 → 148,426），质量无显著损失。
- 难度分层：easy 触发 14/22（64%，节省 30.5%）、medium 11/20（55%，节省 11.9%）、hard 7/18（39%，无净收益）。
- 独立 gold 锚定 F1@20 审计（θ = 0.70）：0.475 vs. 0.480（宏观差 0.005），见论文 Table 3b。

## 数据字段（full_experiment_results.json）

每个查询记录包含：

- `query_id` / `query` / `difficulty` / `domain`：查询标识、内容、难度（easy/medium/hard）、领域
- `strategy` / `strategy_name`：策略（agent = LLM Agent，agent_qa = QA-Agent）
- `num_papers`：检索到的论文数
- `cost`：token 消耗（total_tokens / api_calls / rounds / elapsed），QA-Agent 含 `quality_history`（每轮质量感知）
- `judge`：LLM 裁判五维打分（relevance / coverage / recency / authority / diversity / overall），含分数与理由
- `scoring`：`continuous`

顶层 `meta.threshold = 0.5` 对应论文主实验采用的阈值 θ_q = 0.50。

## 复现

数据由 `experiments/` 目录下的脚本产生：
- 主实验：`run_full_experiment.py`（并行跑 benchmark_data.json 的 60 查询）
- 消融：`ablation_experiment.py`
- 统计检验：`statistical_test.py` / `final_statistical_test.py`
- 效应量/功效分析：`effect_size_analysis.py` / `power_analysis.py`

主代码位于 [zxcvbnm101044/acadsearch](https://github.com/zxcvbnm101044/acadsearch)。

> 注：本仓库数据仅供论文复现与学术使用。文件名与字段结构以最新的 `full_experiment_results.json`（2026-08-09）为准，与论文 "Quality-Aware Autonomous Search Agent for Academic Literature Retrieval"（The Journal of Supercomputing 投稿版）一致。
