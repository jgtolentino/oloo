# EPIC – Databricks-Style Console + OSS Lakehouse Automation (UPDATED)

## Epic Objective
Deliver a Databricks-style workspace console and workflow automation platform using only OSS + standard infra.

## Why This Epic Exists
- Remove DBU-driven cost and lock-in for control plane (workflows, jobs, governance)
- Standardize workflows as code (GitOps)
- Maintain portability across infra providers

## Epic Deliverables (UI included)
- Databricks-style Console UI:
  - Home onboarding
  - Catalog browser
  - Jobs & runs + log viewer
  - SQL editor + warehouses + history
  - Compute profiles
- Control Plane API backing all views
- Airflow DAG generation from Job YAML
- Iceberg + Trino wired end-to-end
- Observability + lineage pointers

## Full Deliverables
- Control Plane:
  - API + UI for workspaces, jobs, runs, logs
- Orchestration:
  - Airflow-backed execution with DAG generation from YAML job specs
- Lakehouse:
  - Iceberg on object storage with medallion sample pipeline
- SQL:
  - Trino integration + query runner
- Observability:
  - Prometheus/Grafana dashboards and service health views
- Lineage + ML (Phase 6):
  - OpenLineage integration
  - MLflow integration

## Definition of Done
- UI parity tasks (Phase 3) complete and UAT core UI scenarios pass
- Dev stack (compose) runs end-to-end pipeline
- Staging stack (helm) deploys healthy
- UAT "Core" scenarios pass
- Success criteria met for MVP
