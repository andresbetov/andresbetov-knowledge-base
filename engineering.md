---
title: "Engineering"
slug: engineering
last_verified: 2026-11-06
---

# Engineering

Engineering principles applied across the project pages, with links to where each appears.

## Principles

| Principle | In practice | Evidence |
| --- | --- | --- |
| Verify before done | Tests and gates decide completion | [Elara](projects/elara-app.md) and [Clustering](projects/hierarchical-clustering-portfolio-selector.md) and [SP500](projects/sp500-dataset-ml-pipeline.md) |
| Record decisions | Architecture and implementation decisions are written down; ADRs supersede rather than rewrite | [Clustering](projects/hierarchical-clustering-portfolio-selector.md) and [SP500](projects/sp500-dataset-ml-pipeline.md) |
| Make runs reproducible | Lockfiles and run fingerprints in both; artifact hashes and one shot holdout lock in SP500 | [Clustering](projects/hierarchical-clustering-portfolio-selector.md) and [SP500](projects/sp500-dataset-ml-pipeline.md) |
| Keep boundaries explicit | Database per service and phase artifacts | [Elara](projects/elara-app.md) and [SP500](projects/sp500-dataset-ml-pipeline.md) |
| Automate the gates | CI runs tests; Elara adds Compose and configuration checks | [Elara](projects/elara-app.md) and [Clustering](projects/hierarchical-clustering-portfolio-selector.md) |
| Keep one home per fact | Each fact lives in one document and links outward | [Elara](projects/elara-app.md) and [SP500](projects/sp500-dataset-ml-pipeline.md) |
| Report results honestly | Out of sample tables and rejected calibration and stated bias | [Clustering](projects/hierarchical-clustering-portfolio-selector.md) and [SP500](projects/sp500-dataset-ml-pipeline.md) |
| Prefer the simplest mechanism | Remove unused acceleration in Clustering and keep the Elara implementation readable | [Clustering](projects/hierarchical-clustering-portfolio-selector.md) and [Elara](projects/elara-app.md) |
| Fail loudly | Named rejections and fail fast checks instead of silent skips | [Clustering](projects/hierarchical-clustering-portfolio-selector.md) and [SP500](projects/sp500-dataset-ml-pipeline.md) |

Back to [README](README.md).
