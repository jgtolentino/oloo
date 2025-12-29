# EPIC – Replace Databricks Workflows With Self-Hosted Lakehouse Automation

## Epic Objective
Deliver a self-hosted platform that replicates Databricks workflow automation and lakehouse operations using only OSS components and standard infrastructure.

## Why This Epic Exists
- Remove DBU-driven cost and lock-in for control plane (workflows, jobs, governance)
- Standardize workflows as code (GitOps)
- Maintain portability across infra providers

## Epic Deliverables
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
- Dev stack (compose) runs end-to-end pipeline
- Staging stack (helm) deploys healthy
- UAT "Core" scenarios pass
- Success criteria met for MVP
