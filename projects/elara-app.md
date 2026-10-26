---
title: "Elara App"
slug: elara-app
type: backend-system
status: active
version: 1
repos: ["https://github.com/elara-app/api-gateway", "https://github.com/elara-app/discovery-service", "https://github.com/elara-app/config-service", "https://github.com/elara-app/inventory-service", "https://github.com/elara-app/unit-of-measure-service"]
org: "https://github.com/elara-app"
source_of_truth: "https://github.com/elara-app"
stack: ["java-21", "spring-boot", "spring-cloud", "eureka", "config-server"]
last_verified: 2026-10-26
---

# Elara App

## 1. Intent
Elara App is a Spring Cloud microservice platform for inventory and unit catalogs.
Gateway routes edge traffic to inventory and unit services through Eureka discovery.
Config Server serves versioned non secret settings from centralized configuration.
Vault supplies dev credentials and RabbitMQ carries bus refresh events.
Compose stacks provide source and published demo runtimes with Postgres and Vault.
Goal is reproducible local orchestration with clear service boundaries and contracts.

## 2. Non-Goals
- Not a production deployment with TLS or hardening.
- Not a stock ledger or purchasing workflow.
- Not a tutorial for Spring or Docker.
- Not a home for production secrets in Git.

## 3. Source Map
| Area | Repo/Path | Entrypoint |
| --- | --- | --- |
| Gateway | https://github.com/elara-app/api-gateway | README.md:routes |
| Discovery | https://github.com/elara-app/discovery-service | README.md:registry |
| Config | https://github.com/elara-app/config-service | README.md:endpoints |
| Inventory | https://github.com/elara-app/inventory-service | README.md:items |
| Units | https://github.com/elara-app/unit-of-measure-service | README.md:catalog |
| Central Config | https://github.com/elara-app/centralized-configuration | README.md:matrix |
| Infra | https://github.com/elara-app/infrastructure | docs/operations.md:stacks |

## 4. System at a Glance
- Edge gateway forwards inventory and unit traffic with StripPrefix routing.
- Eureka registry tracks gateway and business instances in memory only.
- Config Server serves app profile files from main branch on demand.
- Inventory owns item master data and checks unit ids over Eureka.
- Unit service owns unit catalog and status catalog with no outbound calls.
- Central configuration owns routes and profile matrix and dev secret bootstrap.
- Infra owns source and published Compose stacks with health gated startup.
- Postgres holds runtime data in ephemeral tmpfs and Vault holds dev secrets.

## 5. Key Numbers
No verified numbers yet — see Source Map.

## 6. Engineering Discipline
- Tests run with test profile and Maven wrapper through verify.
- Changes keep docs and code aligned across owned repositories.
- Reviews happen before publish with static Compose render checks.
- Secrets stay out of Git and Vault paths stay exact per app.
- Gateway route edits coordinate with central config and infra checks.

## 7. Reproduce
1. Open `centralized-configuration/README.md` and review profile matrix.
2. Render `compose.yml` with `docker compose config --quiet` for static check.
3. Query `config-service` with `curl` for inventory dev environment.
4. Verify gateway with `curl` on swagger and item collection paths.

## 8. Limitations
- Prod profile files are sparse placeholders and not deployable.
- Gateway has no refresh endpoint so route edits need restart.
- State is ephemeral with tmpfs Postgres and in memory Vault.
- Published demo is not hardened and needs no production use.
- Test files in central config do not drive Maven test runs.

## 9. History
- Created from project template.
- Intent drafted from platform docs.
- Source Map added for services and config.
- Glance reviewed for runtime boundaries.
- Key Numbers left unverified pending measurement.
- Discipline confirmed from contributor guides.
- Reproduce tried with static checks only.
- Limitations noted from current docs.
No publish events yet.
