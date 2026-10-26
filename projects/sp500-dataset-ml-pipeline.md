---
title: "SP500 Dataset ML Pipeline"
slug: sp500-dataset-ml-pipeline
type: data-pipeline
status: active
version: 1
repos: ["https://github.com/andresbetov/sp500-dataset-ml-pipeline"]
source_of_truth: "https://github.com/andresbetov/sp500-dataset-ml-pipeline"
stack: ["python-3.12", "pandas", "xgboost", "scikit-learn", "pyarrow", "uv"]
last_verified: 2026-10-26
---

# SP500 Dataset ML Pipeline

## 1. Intent
Predicts 5-day realized volatility for S&P 500 constituents from price and volume history.
Target is Yang-Zhang per-day sigma over the next five trading days and not annualized.
Model trains one XGBoost regressor per temporal fold on 37 features with no ticker identity.
Scoring uses QLIKE against HAR-RV under date-group expanding folds with a train-only purge.
Intended use is research and benchmarking under a fixed protocol and not production risk.
Source universe is survivors-only so delisted names are absent and long-horizon returns are biased upward.
Final state is v0.2.0 and the QLIKE edge is established under this protocol only.

## 2. Non-Goals
- Not a production risk or trading system.
- Not a tool for VaR or position sizing.

## 3. Source Map
| Area | Repo/Path | Entrypoint |
| --- | --- | --- |
| Dataset Phase 1 | https://github.com/andresbetov/sp500-dataset-ml-pipeline | packages/ml/dataset/main.py:generate_featured_dataset |
| Model Phases 2 to 5 | https://github.com/andresbetov/sp500-dataset-ml-pipeline | packages/ml/model/main.py:main |
| Dataset Reference | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/pipelines/dataset.md:overview |
| Model Protocol | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/pipelines/model.md:protocol |
| Runbook | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/operations.md:runbook |
| Results Guide | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/results.md:reading |
| Model card | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/model-card.md:caveats |
| Decisions | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/adr:index |
| CLI contract | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/reference/cli.md:flags |

## 4. System at a Glance
- Phase 1 writes `data/processed/dataset.parquet` from the pinned CSV.
- Phases 2 to 5 write inputs and folds and models and charts.
- Generated outputs are gitignored and never committed.
- Phases couple only through on disk artifacts and fail fast when inputs are missing.
- Holdout is one shot and its lock is written before any metric.

## 5. Key Numbers
- CV fold-equal QLIKE is 0.058485 for the model versus 0.107674 for HAR (evaluation, 2026-10-20, evaluation_summary.json).
- CV edge is 0.049189 or 45.68 percent lower across five folds and 2094049 scored rows (evaluation, 2026-10-20, evaluation_summary.json).
- Locked holdout QLIKE is 0.068804 versus 0.094570 for HAR (holdout, 2026-10-20, holdout_report.json).
- Holdout edge is 0.025766 or 27.25 percent lower on 234473 scored rows (holdout, 2026-10-20, holdout_report.json).
- Holdout Diebold-Mariano is -7.30 in favor of the model with a 95 percent edge interval of 0.0188 to 0.0327 (report, 2026-10-20, docs/reports/model-performance-and-rigor.md).
- All five fold edges are positive from 0.0205 to 0.0773 (evaluation, 2026-10-20, evaluation_summary.json).
- Dataset snapshot holds 2.67M rows and 469 tickers and 6504 dates (schema snapshot, 2026-10-25, docs/reference/dataset-schema.md).
- 37 features split as 13 price plus 4 volume plus 5 trend plus 15 volatility (feature table, 2026-10-26, docs/reference/dataset-schema.md).
- Five expanding folds reserve 500 dates as holdout (protocol, 2026-10-26, docs/pipelines/model.md).
- Hermetic suite collects 270 tests as the CI gate and full suite collects 275 (CI gate, 2026-10-26, docs/operations.md).

## 6. Engineering Discipline
- Runs compileall plus hermetic pytest on every push and pull request.
- Runs a dataset smoke phase on CI to keep Phase 1 executable.
- Keeps model smoke as a manual workflow because it needs generated data.
- Verifies every loaded artifact against a size and SHA256 registry.
- Writes a run manifest and an append only run log with git and lock hashes.
- Locks the holdout before any metric and raises on a second evaluation.
- Freezes ADRs and supersedes them instead of rewriting numbers.
- Separates current state docs from the dated evaluation report.

## 7. Reproduce
1. With Kaggle credentials and network access run `uv sync` then `uv run python packages/ml/dataset/main.py` for Phase 1.
2. Run `uv run python -m packages.ml.model.main --dry-run` to log planned phases.
3. Run `uv run python -m packages.ml.model.main` to train five folds and write results.
4. Read `packages/ml/model/artifacts/results/evaluation_summary.json` for headline QLIKE; artifacts outrank charts.

## 8. Limitations
- Survivors only universe omits delisted names and biases long horizon returns up.
- Calibration is rejected by Mincer-Zarnowitz and no predictive distribution exists.
- Single seed with one locked 500 date window leaves uncertainty unestimated.
- No economic test covers costs or P&L or value at risk.
- No tail safety is established; deciles six to nine have higher QLIKE than HAR.
- HAR is the only benchmark and no GARCH or ensemble is tested.
- Fold 2 hit the round cap so convergence is not demonstrated.
- Regime labels are hardcoded calendar buckets so regime tables are descriptive only.

## 9. History
- Eight ADRs record the target and universe and coupling and verification decisions.
- Model card added with intended use and standing caveats.
- Locked holdout evaluated once on 2026-10-20.
- Docs split into current state and dated evaluation report.
- v0.2.0 tagged on 2026-10-25 at commit f861a9a.
No external announcements recorded.
