# Tasks – OpenLake Orchestrator Backlog (UPDATED: UI production parity)

## Phase 1 — Bootstrap
- [T-01] Scaffold repo layout per planning.md
- [T-02] Add dev stack docker compose (Airflow, Spark, Trino, MinIO, Postgres, OLO API, OLO UI)
- [T-03] Add Helm skeleton (namespaces, values, placeholders for all services)
- [T-04] Add UI spec document (ui.md) and wire as source of truth

## Phase 2 — Control Plane API
- [T-10] Implement API service scaffold (health, config, auth stub)
- [T-11] Workspace APIs: create/list/get/update/delete
- [T-12] Job APIs: apply/list/get/delete (YAML spec)
- [T-13] Run APIs: trigger/status/list + log pointers
- [T-14] Persistence layer in Postgres (migrations)
- [T-15] RBAC middleware + role claims model (stub first)

## Phase 3 — UI Console (Databricks-style)
- [T-20] App shell: left nav + top bar + workspace switcher + global search
- [T-21] Home page: onboarding cards + connect data + create pipeline CTAs
- [T-22] Recents page: recent jobs, queries, tables
- [T-23] Catalog: tree + table list + table detail tabs
- [T-24] Jobs: list + filters + job detail + YAML viewer/editor (RBAC gated)
- [T-25] Runs: run detail + task list + status timeline
- [T-26] Logs viewer: chunked fetch + virtualization + per-task logs
- [T-27] SQL editor: Monaco + results grid + saved queries + history
- [T-28] Warehouses: Trino resource groups list + status (admin edit later)
- [T-29] Compute: Spark profiles list + profile detail

## Phase 4 — Orchestration Integration
- [T-30] Job YAML schema + validation (strict)
- [T-31] Translate Job YAML → Airflow DAG generator
- [T-32] Airflow sync mechanism (push DAGs + refresh)
- [T-33] Run trigger: API triggers Airflow DAG run
- [T-34] Status/log ingestion: poll Airflow + aggregate run state

## Phase 5 — Lakehouse + SQL
- [T-40] Iceberg bootstrap: catalog + warehouse path on MinIO
- [T-41] Spark + Iceberg connector wiring (dev + helm)
- [T-42] Trino Iceberg catalog wiring (dev + helm)
- [T-43] SQL runner endpoint (Trino) + UI query page
- [T-44] Medallion sample pipeline: bronze→silver→gold
- [T-45] Sample datasets loader (one-click from Home)

## Phase 6 — Lineage + ML
- [T-50] OpenLineage collector deployment + plumbing
- [T-51] Emit lineage events from Spark/dbt tasks
- [T-52] Lineage API: dataset/job graph retrieval
- [T-53] Lineage UI graph view
- [T-54] MLflow deployment + API linkouts
- [T-55] Link job runs ↔ MLflow runs

## Phase 7 — Governance + Production Hardening
- [T-60] RBAC model + workspace roles enforced in UI and API
- [T-61] OIDC SSO integration
- [T-62] Audit log for config changes and run triggers
- [T-63] HA control plane deployment + readiness checks
- [T-64] Backup/restore runbook (Postgres + object store metadata)
- [T-65] UI accessibility + performance baseline gates (CI)
