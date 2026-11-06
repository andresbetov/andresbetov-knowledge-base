---
title: "SP500 Dataset ML Pipeline"
slug: sp500-dataset-ml-pipeline
type: data-pipeline
status: active
version: 5
repos: ["https://github.com/andresbetov/sp500-dataset-ml-pipeline"]
source_of_truth: "https://github.com/andresbetov/sp500-dataset-ml-pipeline"
stack: ["python-3.12", "pandas", "xgboost", "scikit-learn", "pyarrow", "uv"]
last_verified: 2026-11-06
---

# SP500 Dataset ML Pipeline

## 1. Intent
Predicts 5-day realized volatility for S&P 500 constituents from price and volume history.
Target is Yang-Zhang per-day sigma over the next five trading days and not annualized.
Model trains one XGBoost regressor per temporal fold on 37 features with no ticker identity.
Scoring uses QLIKE against HAR-RV under date-group expanding folds with a train-only purge.
Intended use is research and benchmarking under a fixed protocol and not production risk.
Source universe is survivors-only so delisted names are absent and long-horizon returns are biased upward.
Current state is v0.2.0 plus two commits after the tag (main at a25c9b8); the protocol reports a lower average QLIKE than HAR, not a general edge.
Solo design and implementation from April to October 2026.

## 2. Non-Goals
- Not a production risk or trading system.
- Not a tool for VaR or position sizing.

## 3. Source Map
| Area | Repo/Path | Entrypoint |
| --- | --- | --- |
| Dataset Phase 1 | https://github.com/andresbetov/sp500-dataset-ml-pipeline | packages/ml/dataset/main.py:generate_featured_dataset |
| Model Phases 2 to 5 | https://github.com/andresbetov/sp500-dataset-ml-pipeline | packages/ml/model/main.py:main |
| Dataset Reference | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/pipelines/dataset.md#purpose-and-contract |
| Model Protocol | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/pipelines/model.md#evaluation |
| Runbook | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/operations.md#normal-run |
| Results Guide | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/results.md#reading-the-headline |
| Model card | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/model-card.md#results-and-caveats |
| Decisions | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/adr/index.md |
| CLI contract | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/reference/cli.md#model-flags |
| Technical report | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/reports/model-performance-and-rigor.md#1-executive-summary-and-claim-card |
| Evaluation | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/reports/model-performance-and-rigor.md#6-cross-validation-performance |
| Holdout | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/reports/model-performance-and-rigor.md#7-holdout-performance |

## 4. System at a Glance
- Phase 1 writes `data/processed/dataset.parquet` from the pinned CSV.
- Phases 2 to 5 write inputs and folds and models and charts.
- Generated outputs are gitignored and never committed.
- Decision: phases couple through artifacts and rerun independently; Phase 2 fails without Phase 1 and Phase 1 writes into the model artifact tree (ADR 0003, 2026-10-17, docs/adr/0003-phase-coupling-via-artifacts.md).
- Decision: one shot locked holdout prevents repeated tuning per checkout; a logged force override exists and a fresh checkout can create a new lock (runbook, 2026-10-26, docs/operations.md#locked-holdout).
- Decision: purged temporal folds prevent leakage and reduce training rows near each test block (protocol, 2026-10-26, docs/pipelines/model.md#phase-3--temporal-cross-validation).

## 5. Key Numbers
- CV fold-equal QLIKE is 0.058485 for the model versus 0.107674 for HAR (evaluation, 2026-10-20, docs/reports/model-performance-and-rigor.md#6-cross-validation-performance).
- CV fold-equal edge is 0.049189 or 45.68 percent lower across five folds and 2094049 scored rows (evaluation, 2026-10-20, docs/reports/model-performance-and-rigor.md#6-cross-validation-performance).
- Locked holdout QLIKE is 0.068804 versus 0.094570 for HAR (holdout, 2026-10-20, docs/reports/model-performance-and-rigor.md#7-holdout-performance).
- Holdout edge is 0.025766 or 27.25 percent lower on 234473 scored rows (holdout, 2026-10-20, docs/reports/model-performance-and-rigor.md#7-holdout-performance).
- Holdout Diebold-Mariano is -7.30 in favor of the model with a 95 percent edge interval of 0.0188 to 0.0327 (report, 2026-10-20, docs/reports/model-performance-and-rigor.md).
- All five fold edges are positive from 0.0205 to 0.0773 (evaluation, 2026-10-20, docs/reports/model-performance-and-rigor.md#6-cross-validation-performance).
- Dataset snapshot holds 2.67M rows and 469 tickers and 6504 dates (schema snapshot, 2026-10-25, docs/reference/dataset-schema.md).
- 37 features split as 13 price plus 4 volume plus 5 trend plus 15 volatility (feature table, 2026-10-26, docs/reference/dataset-schema.md).
- Five expanding CV folds plus a separately reserved 500-date holdout (protocol, 2026-10-26, docs/pipelines/model.md#phase-3--temporal-cross-validation).
- Hermetic suite collects 270 tests as the CI gate and full suite collects 275 (CI gate, 2026-10-26, docs/operations.md).
- Results are relative average QLIKE only; calibration is rejected and high-volatility deciles are worse and fold 2 hit the round cap (evaluation, 2026-10-20, docs/reports/model-performance-and-rigor.md).
- Evidence rests on one seed and one 500-date holdout; inputs are price and volume only and economic value is untested (evaluation, 2026-10-20, docs/reports/model-performance-and-rigor.md).

## 6. Engineering Discipline
- Runs compileall plus hermetic pytest on pushes and pull requests to develop and main (workflow, 2026-10-26, .github/workflows/checks.yml).
- Runs a dataset smoke phase on CI to keep Phase 1 executable (workflow, 2026-10-26, .github/workflows/checks.yml).
- Keeps model smoke as a manual workflow because it needs generated data (workflow, 2026-10-26, .github/workflows/model-smoke.yml).
- Verifies every live joblib load against a size and SHA256 registry (runbook, 2026-10-27, docs/operations.md#run-state-files).
- Writes a run manifest and an append only run log with git and lock hashes (protocol, 2026-10-26, docs/pipelines/model.md#phase-4--training).
- Locks the holdout before any metric and raises on a second evaluation unless a logged force reason is supplied (runbook, 2026-10-27, docs/operations.md#locked-holdout).
- Freezes ADRs and supersedes them instead of rewriting numbers (index, 2026-10-26, docs/adr/index.md).
- Separates current state docs from the dated evaluation report (docs, 2026-10-26, docs/README.md).
- The 270-test suite is model-only; CI runs a Phase 1 smoke and Phase 2 records the dataset hash (workflow and protocol, 2026-10-27, .github/workflows/checks.yml and docs/pipelines/model.md#phase-2--feature-selection).

## 7. Reproduce
1. With Kaggle credentials and network access run `uv sync` then `uv run python packages/ml/dataset/main.py` for Phase 1.
2. Run `uv run python -m packages.ml.model.main --dry-run` to log planned phases.
3. Run `uv run python -m packages.ml.model.main` to train five folds and write results.
4. Read `packages/ml/model/artifacts/results/evaluation_summary.json` for headline QLIKE; artifacts outrank charts.
5. Read the committed technical report for holdout numbers; do not run `--evaluate-holdout` or `--force-holdout` to audit, because the 2026-10-20 evaluation is canonical and a fresh checkout can create a new lock.

## 8. History
- Eight ADRs record the target and universe and coupling and verification decisions.
- Model card added with intended use and standing caveats.
- Locked holdout evaluated once on 2026-10-20.
- Docs split into current state and dated evaluation report.
- v0.2.0 tagged on 2026-10-25 at commit f861a9a.

No external announcements recorded.

Back to [README](../README.md).
