---
title: "Elara App"
slug: elara-app
type: backend-system
status: active
version: 1
repos: ["https://github.com/elara-app/api-gateway", "https://github.com/elara-app/discovery-service", "https://github.com/elara-app/config-service", "https://github.com/elara-app/inventory-service", "https://github.com/elara-app/unit-of-measure-service"]
source_of_truth: "https://github.com/elara-app"
stack: ["java-21", "spring-boot", "spring-cloud", "eureka", "config-server"]
last_verified: 2026-10-26
---

# Elara App

## 1. Intent
Elara App is a completed Spring Cloud reference platform at v1.3.1 for inventory and unit catalogs.
Five runtime services plus two support repositories form one Docker Compose system.
Gateway routes edge traffic to inventory and units through Eureka discovery.
Config Server serves versioned non secret settings and Vault supplies dev credentials.
RabbitMQ carries config refresh events and health gates order startup.
Two delivery modes exist as a source build of nine Compose services and a published demo of ten Compose services.
Goal is a readable reference implementation rather than a production deployment.

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
| Profile | https://github.com/elara-app/.github | profile/README.md:run-the-demo |
| Inventory tests | https://github.com/elara-app/inventory-service | TESTING_GUIDE.md:suite |
| Unit tests | https://github.com/elara-app/unit-of-measure-service | TESTING_GUIDE.md:suite |

## 4. System at a Glance
- Edge gateway uses explicit routes with StripPrefix 3 and a restricted locator.
- Eureka registry is single node and in memory with no replication.
- Config Server serves profile files from the main branch on demand.
- Precedence runs CLI then JVM then environment then config data then defaults.
- Inventory owns item master data and validates unit ids over HTTP.
- Unit service owns unit and status catalogs with no outbound calls.
- Central configuration owns routes and the profile matrix and Vault paths.
- Infra owns source and published Compose stacks with health gated startup.
- Source stack runs nine Compose services and published demo runs ten because its Vault loader is default.
- State is ephemeral with tmpfs Postgres and in memory Vault.

## 5. Key Numbers
- 379 documented tests as 128 inventory plus 229 unit plus 20 gateway plus 1 discovery plus 1 config (suite table, 2026-10-26, org profile README).
- Version 1.3.1 agrees across VERSION and five service poms (version script, 2026-10-26, infra/scripts/check-release-version.sh).
- Coverage gates enforce 80 percent line plus 70 percent branch with no class at zero coverage (plugin config, 2026-10-26, inventory-service/pom.xml).
- Inventory JaCoCo reports 97.69 percent line and 77.78 percent branch (artifacts, 2026-10-22, target/site/jacoco/jacoco.csv).
- Unit service JaCoCo reports 100 percent line and branch (artifacts, 2026-10-22, target/site/jacoco/jacoco.csv).
- Seed data holds 10 items plus 5 units plus 3 statuses (feature table, 2026-10-26, org profile README).

## 6. Engineering Discipline
- CI runs the Maven verify gate on Java 21 through reusable workflows (test workflow, 2026-10-26, service .github/workflows/test.yml).
- Config repository validates every YAML file and requires the Eureka URI placeholder (validation workflow, 2026-10-26, centralized-configuration/.github/workflows/validate-config.yml).
- Infra renders Compose files and asserts services and ports and healthchecks (compose checks, 2026-10-26, infra/.github/workflows/compose-checks.yml).
- Publishing promotes a digest pinned sha artifact and runs smoke and flow checks after the latest tag (publish workflow, 2026-10-26, infra/.github/workflows/publish-compose.yml).
- Coverage gates exclude config and DTO packages and application classes by policy (JaCoCo config, 2026-10-26, service pom.xml).
- Bus refresh covers business services only and gateway route edits need a restart (config doc, 2026-10-26, centralized-configuration/README.md).
- Compose defaults replace env files and the release version script stays manual (operations doc, 2026-10-26, infra/docs/operations.md).

## 7. Reproduce
1. Use Docker Compose v2.30 or newer, point `ELARA` at `oci://ghcr.io/elara-app/elara-demo:1.3.1` for the demo or at an `infrastructure/compose.yml` checkout for a source build, then run `docker compose -f "$ELARA" up -d --wait --wait-timeout 900`.
2. For source builds needing dev secrets run `docker compose -f "$ELARA" --profile bootstrap up vault-loader`.
3. Open `http://127.0.0.1:8080/swagger-ui.html` for the aggregated API surface.
4. Confirm seeds through the gateway with `curl` on `/api/v1/inventory/item/` and `/api/v1/uom/` and `/api/v1/uom/status/` expecting 10 and 5 and 3.
5. Follow logs with `docker compose -f "$ELARA" logs -f` and stop with `docker compose -f "$ELARA" down` noting data is ephemeral.

## 8. Limitations
- Demo and not production: known dev credentials and no TLS and single registry (infra README, 2026-10-26, infra/README.md).
- All state is ephemeral with no backup or persistence path (operations doc, 2026-10-26, infra/docs/operations.md).
- Gateway has no auth and no rate limiting and no retry or circuit breaker (gateway README, 2026-10-26, api-gateway/README.md).
- Gateway routing has no integration tests for predicates and StripPrefix (gateway README, 2026-10-26, api-gateway/README.md).
- Inventory maps only 404 from the unit service and turns other failures into 500 (inventory README, 2026-10-26, inventory-service/README.md).
- Unit service has no audit trail and does not enforce status usability on assignment (unit README, 2026-10-26, unit-of-measure-service/README.md).
- Prod profiles are placeholders and schema uses ddl-auto instead of migrations (config README, 2026-10-26, centralized-configuration/README.md).
- Vault loader skips existing paths and cannot repair drifted secrets (operations doc, 2026-10-26, infra/docs/operations.md).
- Published latest tag floats and VERSION agreement is unchecked by CI (infra README, 2026-10-26, infra/README.md).

## 9. History
- v1.3.1 published with five runtime services and two support repositories.
- Source and published stacks split; the published OCI artifact pins image digests.
- Vault bootstrap and config refresh bus added to the platform.
- Test suites expanded across the five services.
- Coverage gates introduced for inventory and units.
- Org profile rewritten as the current entry point at v1.3.1.
No external announcements recorded.
