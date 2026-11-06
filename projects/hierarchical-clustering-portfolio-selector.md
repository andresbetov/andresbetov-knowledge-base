---
title: "Hierarchical Clustering Portfolio Selector"
slug: hierarchical-clustering-portfolio-selector
type: ml-research
status: active
version: 5
repos: ["https://github.com/andresbetov/hierarchical-clustering-portfolio-selector"]
source_of_truth: "https://github.com/andresbetov/hierarchical-clustering-portfolio-selector"
stack: ["python-3.11","numpy","pandas","scipy","scikit-learn","yfinance","uv"]
last_verified: 2026-11-06
---

# Hierarchical Clustering Portfolio Selector

## 1. Intent
Turns a configurable equity universe into an interpretable portfolio with hierarchical clustering.
Uses signed distance with quasi-diagonalization and recursive bisection without matrix inversion.
Enforces concentration bounds with cyclic Dykstra projections and hard post-run verification.
Runs ingest and filter and align and cluster plus allocate and constrain as one deterministic run.
Serves researchers and engineers studying risk-based allocation with walk-forward checks.
Contrasts HRP against 1/N and inverse volatility on out-of-sample folds.
Current state is main at commit 9cbe1b2; package version remains v0.1.0 and the tag predates the JSON report work.
Solo design and implementation from April to October 2026.

## 2. Non-Goals
- Not a price predictor and not investment advice.
- Not a live trading system with costs and taxes and capacity modeling.
- Not a point-in-time universe with delistings and IPOs handled.
- Not a mean-variance-first optimizer; HRP is the default and the only method walk-forward validated.
- Not leverage or shorting capable; volatility targeting was removed under ADR 001.
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
| Decisions | https://github.com/andresbetov/hierarchical-clustering-portfolio-selector | docs/adr/README.md |
| Diagnostics | https://github.com/andresbetov/hierarchical-clustering-portfolio-selector | docs/diagnostics-catalog.md |
| Specs | https://github.com/andresbetov/hierarchical-clustering-portfolio-selector | openspec/specs |
| Evidence | https://github.com/andresbetov/hierarchical-clustering-portfolio-selector | README.md#results |

## 4. System at a Glance
- Turns the configured universe into risk-based weights with HRP by default.
- Keeps Sharpe threshold 0.3 and volatility cap 0.27 and overlap ratio 0.9.
- Validates with a 250-day train and 60-day test and 5-day embargo walk-forward.
- Depends on yfinance at runtime with an optional parquet cache and offline tests by injection.
- Fingerprints each run from config and engine version and universe and window with no clock input.
- Decision: signed correlation distance keeps hedges apart; the legacy absolute distance remains for historical reproduction and adds a second code path (ADR 002, 2026-08-26, docs/adr/002-correlation-distance-choice.md).
- Decision: HRP avoids covariance inversion and gives up mean-variance optimization (ADR 003, 2026-08-26, docs/adr/003-hrp-adoption.md).
- Decision: apply Dykstra after allocation to keep one shared long-only constraint path; this sacrifices exact HRP risk balance (ADR 003 addendum, 2026-09-01, docs/adr/003-hrp-adoption.md).

## 5. Key Numbers
- 372 collected tests from 365 functions plus 7 parametrized cases (pytest collect, 2026-10-26, tests).
- 89.18 percent combined coverage against an 85 percent gate (init.sh gate result, 2026-10-02, progress.md).
- 58 tracked features done on current HEAD (tracker, 2026-10-02, feature_list.json).
- 16 walk-forward folds evaluated (walk-forward run, 2026-09-10, README.md).
- Median OOS Sharpe is 0.502 HRP and 0.521 inverse volatility and 0.456 equal weight; the HRP gap is inside fold dispersion (walk-forward median, 2026-09-10, README.md).
- Showcase run covers 1253 observations and 6 of 12 survivors (engine run, 2026-09-10, README.md).
- The showcase window favors inverse volatility; no costs are modeled and turnover is uncontrolled (walk-forward result, 2026-09-10, README.md).
- Five of 16 folds needed relaxed bounds and median drift implies roughly 35 percent one way turnover (walk-forward run, 2026-09-10, README.md).
- Sharpe uses log returns and the 12-name universe is survivors as of 2026 so medians are biased upward (method note, 2026-09-10, README.md).
- Thresholds use the same walk-forward design (ADR 007, 2026-09-09, docs/adr/007-filter-threshold-recalibration.md).

## 6. Engineering Discipline
- Runs `./init.sh` for sync plus pytest plus ruff plus pyright plus compile check (script, 2026-10-26, init.sh).
- Keeps CI on Python 3.11 plus 3.12 plus 3.13 with frozen lockfile (workflow, 2026-10-26, .github/workflows/ci.yml).
- Uses Hypothesis property tests for numeric and allocation invariants (suite, 2026-10-26, tests/test_properties.py).
- Tests anti leakage by mutating the out of sample slice and freezing weights (suite, 2026-10-26, tests/test_walk_forward.py).
- Records decisions in append-only ADRs where corrections are superseded rather than rewritten (index, 2026-10-26, docs/adr/README.md).
- Tracks 13 live OpenSpec capabilities plus archived changes (specs, 2026-10-26, openspec/specs).
- Requires fresh green evidence before marking a feature done (rules, 2026-10-26, AGENTS.md).
- Fails loud when uv is present and ruff or pyright cannot run (script, 2026-10-26, init.sh).

## 7. Reproduce
1. Run `uv sync --frozen` to install locked dependencies.
2. Run `uv run portfolio-run` to build charts and JSON report.
3. Run `uv run portfolio-run --walk-forward` to add out-of-sample section.
4. Open `reports/technical-report.json` and compare the run id and per fold walk forward weights against `docs/results/technical-report-2026-09-10.json`; a fresh window changes both.

## 8. History
- Numba kernels removed with 1e-12 numeric parity under ADR 004.
- v0.1.0 tagged on 2026-09-09 with HRP engine and eight charts.
- Filter thresholds recalibrated to 0.3 and 0.27 under ADR 007.
- JSON report epic closed with schema version 1.
- README is bilingual; ADRs and diagnostics remain Spanish.

No external announcements recorded.

Back to [README](../README.md).
