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
Source universe is survivors-only so delisted names are absent and returns skew upward.

## 2. Non-Goals
- Not a production risk or trading system.
- Not a tool for VaR or position sizing.
- Not a source of calibrated predictive distributions.

## 3. Source Map
| Area | Repo/Path | Entrypoint |
| --- | --- | --- |
| Dataset Phase 1 | https://github.com/andresbetov/sp500-dataset-ml-pipeline | packages/ml/dataset/main.py:main |
| Model Phases 2 to 5 | https://github.com/andresbetov/sp500-dataset-ml-pipeline | packages/ml/model/main.py:main |
| Dataset Deep Ref | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/pipelines/dataset.md:overview |
| Model Protocol | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/pipelines/model.md:protocol |
| Runbook | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/operations.md:runbook |
| Results Guide | https://github.com/andresbetov/sp500-dataset-ml-pipeline | docs/results.md:reading |

## 4. System at a Glance
- Purpose is volatility research described in Intent above.
- Primary users are researchers and auditors reading artifacts.
- Phase 1 writes `data/processed/dataset.parquet` from the pinned CSV.
- Phases 2 to 5 write inputs and folds and models and charts.
- Generated outputs are gitignored and never committed.
- Holdout is opt-in and locked before scoring.

## 5. Key Numbers
- 37 model inputs split as 13 prices plus 4 volume plus 5 trend plus 15 volatility (feature table, 2026-10-26, docs/reference/dataset-schema.md).
- 53 shipped columns hold 37 inputs plus 16 extras (schema contract, 2026-10-26, docs/reference/dataset-schema.md).
- Shipped snapshot holds 2669959 rows with 53 columns (dated snapshot, 2026-10-25, docs/reference/dataset-schema.md).
- Raw source snapshot holds 2703531 rows and 472 tickers (ingestion census, 2026-10-20, docs/reports/model-performance-and-rigor.md).
- Raw span is 2000-01-03 to 2026-02-20 (ingestion census, 2026-10-20, docs/reports/model-performance-and-rigor.md).
- Shipped span is 2000-04-04 to 2026-02-12 with 6504 dates (dated snapshot, 2026-10-25, docs/reference/dataset-schema.md).
- Shipped ticker count in schema snapshot is 469 (dated snapshot, 2026-10-25, docs/reference/dataset-schema.md).
- Default fold count is 5 with 6 date blocks (protocol doc, 2026-10-26, docs/pipelines/model.md).
- Reserved holdout holds 500 unique dates (protocol doc, 2026-10-26, docs/pipelines/model.md).
- Validation tail holds 0.15 of floor-retained training rows (protocol doc, 2026-10-26, docs/pipelines/model.md).
- Target floor is 1e-12 applied before log (protocol doc, 2026-10-26, docs/pipelines/model.md).
- Early stopping waits 30 rounds on validation QLIKE (operations doc, 2026-10-26, docs/operations.md).
- HAR warm-up uses 21 dates and HAC lag uses 4 (operations doc, 2026-10-26, docs/operations.md).
- Phase 5 renders 14 PNGs plus manifest (artifact table, 2026-10-26, docs/operations.md).
- Hermetic suite collects 270 tests and full suite collects 275 (CI gate doc, 2026-10-26, docs/operations.md).

## 6. Engineering Discipline
- Tests run with hermetic suite before each change.
- Review happens before each publish.
- Changes keep docs and code aligned.
- Generated outputs stay gitignored and uncommitted.
- Holdout is never used for verification.

## 7. Reproduce
1. Run `uv sync` then `python packages/ml/dataset/main.py` for Phase 1.
2. Run `python -m packages.ml.model.main --dry-run` to log planned phases.
3. Run `python -m packages.ml.model.main` to train five folds and write results.
4. Read `packages/ml/model/artifacts/results/evaluation_summary.json` for headline QLIKE.

## 8. Limitations
- Survivors-only universe omits delisted names and skews long-horizon returns.
- Price and volume only with no macro or sentiment inputs.
- No sector data so sector dynamics cannot be learned.
- Calendar market regime is a hardcoded bucket and not a model input.
- Calibration is rejected and tail safety is not established.
- Single seed with one 500-date holdout leaves uncertainty unestimated.

## 9. History
- Created from template.
- Intent drafted.
- Source Map added.
- Glance reviewed.
- Key Numbers checked.
- Discipline confirmed.
- Reproduce tried.
- Limitations noted.
No publish events yet.
