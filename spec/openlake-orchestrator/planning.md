# OpenLake Orchestrator – Planning

## Purpose
Deliver a self-hosted, OSS-first workflow automation and lakehouse platform that replicates Databricks Workflows/Jobs/SQL Warehouse semantics using:
Airflow + Spark + Iceberg + Trino + MinIO + MLflow + OpenLineage + Prometheus/Grafana.

## System Components (Target)
| Area | Default | Replaceable With |
|------|---------|------------------|
| Orchestration | Airflow | Temporal / Prefect |
| Compute | Spark (k8s operator or standalone) | Flink / Ray (future) |
| Table Format | Iceberg | Delta / Hudi |
| Object Store | MinIO (S3) | S3 / GCS / ADLS |
| SQL Engine | Trino | ClickHouse / DuckDB |
| Catalog | Hive Metastore or Polaris | Other OSS catalogs |
| Lineage | OpenLineage | Marquez (optional UI) |
| ML | MLflow | N/A |
| Observability | Prometheus + Grafana | Any Prom backend |

## Primary Environments
### dev (local)
- Docker Compose (single-node experience)
- Minimal security (dev defaults)
- Used for fast iteration and demos

### staging (k8s single-node)
- Helm deploy, ingress, TLS optional
- Represents production topology with lower scale

### prod (k8s multi-node HA)
- HA control plane (replicas)
- external object storage (S3/compatible)
- SSO + RBAC enforced

## Repo Layout (Planned)
```text
api/                  # control plane API (FastAPI or Node/TS)
ui/                   # web console
infra/
  compose/            # docker compose stack for dev
  helm/               # helm charts for staging/prod
docs/                 # runbooks and architecture docs
spec/openlake-orchestrator/
```

## Delivery Phases

### Phase 1 — Bootstrap
* repo skeleton
* dev docker compose stack
* helm skeleton

### Phase 2 — Control Plane API
* workspaces CRUD
* jobs CRUD (yaml spec ingest)
* runs API (trigger, status, logs pointers)
* persistence (Postgres)

### Phase 3 — UI Console
* workspace selector
* jobs list + run history
* run detail view + task logs view

### Phase 4 — Workflow Engine Integration
* sync job specs to Airflow DAGs
* trigger runs
* stream status/log pointers

### Phase 5 — Lakehouse + SQL
* Iceberg on MinIO
* Trino catalogs + schemas
* query runner + saved queries (basic)

### Phase 6 — Lineage + ML
* OpenLineage collector wired from jobs
* lineage graph view (dataset ↔ jobs)
* MLflow integrated + linked runs

### Phase 7 — Governance + HA
* OIDC + RBAC
* HA deployment
* audit logs and basic policy controls

## CI/CD (Planned)
* PR checks:
  * validate spec files exist and conform
  * lint + unit tests (api/ui)
  * compose smoke test (optional)
  * helm template lint
* main branch:
  * build images
  * publish charts
  * deploy staging (optional)
