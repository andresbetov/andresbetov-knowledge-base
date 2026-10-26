---
title: "Engineering"
slug: engineering
last_verified: 2026-10-26
---

# Engineering

How I build software: principles applied across the projects in this knowledge base, each with evidence in the project pages.

## Principles

| Principle | In practice | Evidence |
| --- | --- | --- |
| Verify before done | Tests and coverage gates decide completion instead of inspection | [Elara](projects/elara-app.md) and [Clustering](projects/hierarchical-clustering-portfolio-selector.md) and [SP500](projects/sp500-dataset-ml-pipeline.md) |
| Record decisions | ADRs freeze context and later changes supersede instead of rewrite | [Clustering](projects/hierarchical-clustering-portfolio-selector.md) and [SP500](projects/sp500-dataset-ml-pipeline.md) and [thesis](writing.md) |
| Make runs reproducible | Lockfiles and seeds and artifact hashes and one shot holdout locks | [SP500](projects/sp500-dataset-ml-pipeline.md) and [Clustering](projects/hierarchical-clustering-portfolio-selector.md) |
| Keep boundaries explicit | Database per service and phase artifacts and typed contracts | [Elara](projects/elara-app.md) and [SP500](projects/sp500-dataset-ml-pipeline.md) |
| Automate the gates | CI runs tests and Compose checks and configuration validation | [Elara](projects/elara-app.md) and [Clustering](projects/hierarchical-clustering-portfolio-selector.md) |
| Document once | One home per fact with links outward to source repositories | [Elara](projects/elara-app.md) and [SP500](projects/sp500-dataset-ml-pipeline.md) |
| Report results honestly | Out of sample tables and rejected calibration and stated bias | [Clustering](projects/hierarchical-clustering-portfolio-selector.md) and [SP500](projects/sp500-dataset-ml-pipeline.md) |
| Prefer the simplest mechanism | Remove unused acceleration and avoid frameworks without a need | [Clustering](projects/hierarchical-clustering-portfolio-selector.md) and [Elara](projects/elara-app.md) |
| Fail loudly | Named rejections and fail fast checks instead of silent skips | [Clustering](projects/hierarchical-clustering-portfolio-selector.md) and [SP500](projects/sp500-dataset-ml-pipeline.md) |

## Application

The project pages show where each principle appears; the source repositories remain authoritative on conflict.
