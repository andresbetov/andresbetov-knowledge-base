---
title: "Hierarchical Clustering Portfolio Selector"
slug: hierarchical-clustering-portfolio-selector
type: ml-research
status: active
version: 1
repos: ["https://github.com/andresbetov/hierarchical-clustering-portfolio-selector"]
source_of_truth: "https://github.com/andresbetov/hierarchical-clustering-portfolio-selector"
stack: ["python-3.11","numpy","pandas","scipy","scikit-learn","yfinance","uv"]
last_verified: 2026-10-26
---

# Hierarchical Clustering Portfolio Selector

## 1. Intent
Turns a broad equity universe into an interpretable and risk-balanced portfolio with hierarchical clustering.
Uses signed distance with quasi-diagonalization and recursive bisection without matrix inversion.
Enforces concentration bounds with cyclic Dykstra projections and hard post-run verification.
Runs ingest and filter and align and cluster plus allocate and constrain as one deterministic run.
Serves researchers and engineers studying risk-based allocation with walk-forward checks.
Contrasts HRP against 1/N and inverse volatility on data never used for fitting.

## 2. Non-Goals
- Not a price predictor and not investment advice.
- Not a live trading system with costs and taxes and capacity modeling.
- Not a point-in-time universe with delistings and IPOs handled.
- Not a mean-variance optimizer with covariance inversion.
- Not a tutorial for general Python tooling.

## 3. Source Map
| Area | Repo/Path | Entrypoint |
| --- | --- | --- |
| Ingest | https://github.com/andresbetov/hierarchical-clustering-portfolio-selector | portfolio_engine/data/provider.py:YFinanceProvider |
| Filter | https://github.com/andresbetov/hierarchical-clustering-portfolio-selector | portfolio_engine/portfolio/selection.py:apply_asset_filters |
| Allocate | https://github.com/andresbetov/hierarchical-clustering-portfolio-selector | portfolio_engine/portfolio/hrp.py:calculate_hrp_weights |
| Validate | https://github.com/andresbetov/hierarchical-clustering-portfolio-selector | portfolio_engine/validation/walk_forward.py:walk_forward_evaluate |
| Report | https://github.com/andresbetov/hierarchical-clustering-portfolio-selector | portfolio_engine/app/report_json.py:build_technical_report |
| CLI | https://github.com/andresbetov/hierarchical-clustering-portfolio-selector | portfolio_engine/cli.py:main |

## 4. System at a Glance
- Turns a 12 ticker universe into risk-balanced weights with HRP by default.
- Runs ingest then filter then align then cluster plus allocate then constrain.
- Uses signed distance sqrt with 0.5 times 1 minus rho for clustering.
- Keeps Sharpe threshold 0.3 and volatility cap 0.27 and overlap ratio 0.9.
- Emits console weights plus eight charts plus a versioned JSON report.
- Validates with walk-forward train 250 and test 60 and embargo 5.
- Compares HRP against 1/N and inverse volatility on identical out-of-sample returns.
- Depends on yfinance at runtime with parquet cache and offline tests by injection.

## 5. Key Numbers
- 372 offline tests passing (init.sh run, 2026-10-02, progress.md)
- 58 tracked features shipped (count, 2026-10-02, feature_list.json)
- 89.18 percent combined coverage (coverage report, 2026-10-02, progress.md)
- 85 percent coverage gate enforced (coverage gate, 2026-09-05, CONTRIBUTING.md)
- 16 walk-forward folds evaluated (walk-forward run, 2026-09-10, README.md)
- 1253 daily observations in window (engine run, 2026-09-10, README.md)
- 6 of 12 survivors after filter (engine run, 2026-09-10, README.md)
- 0.502 median out-of-sample Sharpe for HRP (walk-forward median, 2026-09-10, README.md)
- 0.456 median out-of-sample Sharpe for 1/N (walk-forward median, 2026-09-10, README.md)
- 0.521 median out-of-sample Sharpe for inverse volatility (walk-forward median, 2026-09-10, README.md)
- 0.94 in-sample Sharpe log convention (engine run, 2026-09-10, README.md)
- 8 diagnostic charts per run (engine run, 2026-09-10, README.md)

## 6. Engineering Discipline
- Runs `./init.sh` for sync plus pytest plus ruff plus pyright plus compile check.
- Keeps CI on Python 3.11 plus 3.12 plus 3.13 with frozen lockfile.
- Requires one feature at a time with fresh green evidence before done.
- Records methods in ADRs and specs in openspec with archived changes.
- Uses deterministic fixtures independent of process hash seed.
- Branches from develop with squash merge and delete after merge.

## 7. Reproduce
1. Run `uv sync --frozen` to install locked dependencies.
2. Run `uv run portfolio-run` to build charts and JSON report.
3. Run `uv run portfolio-run --walk-forward` to add out-of-sample section.
4. Open `reports/technical-report.json` and compare run id and weights.

## 8. Limitations
- Not ready for investment decisions without costs and turnover and taxes.
- Uses fixed survivor universe without delistings and IPOs.
- Depends on yfinance availability and adjustments and coverage.
- Walk-forward covers HRP only and not other allocation methods.
- In-sample diagnostics can look optimistic by construction.
- Threshold choice is not hyperparameter clean per ADR 007.
- Empty universe path fails before report with no graceful exit yet.

## 9. History
- v0.1.0 tagged on 2026-09-09 with HRP engine and eight charts.
- Thresholds recalibrated to 0.3 and 0.27 under ADR 007.
- JSON report epic closed with schema version 1.
- Walk-forward gained production parity and benchmarks.
- Coverage gate set at 85 percent with 230 tests at baseline.
- Counts synced to 372 tests and 58 features on 2026-10-02.
- Develop merged to main with Codespaces verified end to end.
- Docs consolidated across English and Spanish with showcase rewrite.
No publish events yet.
