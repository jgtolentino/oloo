# OpenLake Orchestrator – PRD

Self-Hosted Databricks Workflow Automation, Minus the Lock-In

## 1. Overview

**Product name:** OpenLake Orchestrator (OLO)
**Owner:** Data Platform / Infra Team
**Goal:** Deliver a Databricks-class workflow automation and lakehouse experience using a fully self-hosted, open-source stack (Spark + Iceberg + Airflow/Temporal + Trino + MLflow + OpenLineage), with a cohesive UI and GitOps workflows instead of proprietary Databricks Workflows/Jobs/SQL Warehouse/Unity Catalog.

The system wraps proven open-source components behind:

* A unified control plane and UI
* Opinionated reference architectures (single-node → k8s)
* Git-driven deployments (Spec Kit + IaC)
* First-class workflow automation (orchestration, lineage, ML, monitoring)

This is a **productized version** of the "Replicating Databricks Workflow Automation Without License" stack you outlined: same primitives, but shipped as a coherent platform, not a loose recipe.

---

## 2. Problem & Context

### 2.1 Pain Points With Databricks

* **Lock-in on control layer**
  Spark, Delta, MLflow are open; Workspace, Workflows, Jobs, SQL Warehouse, and full Unity Catalog are proprietary.
* **Opaque pricing (DBUs)**
  Usage-based DBUs make cost prediction hard; infra is abstracted but expensive for steady workloads.
* **Limited portability**
  Moving away from Databricks means re-implementing orchestration, governance, and jobs.
* **UI-centric workflows**
  Heavy use of proprietary UI flows; hard to fully express platform as code.

### 2.2 Pain Points With Current Self-Hosted OSS Approach

* DIY "bag of tools" (Airflow, Spark, Trino, Iceberg, MLflow, Prometheus, OpenLineage) with no opinionated glue.
* Each team re-implements:

  * Docker/K8s topology
  * Lakehouse layout (bronze/silver/gold)
  * DAG scaffolds
  * IaC + CI/CD
* No Databricks-like **job view**, **run history**, **lineage**, and **cost/health view** in one console.

---

## 3. Vision & Positioning

**Vision:**
OpenLake Orchestrator is the "open Databricks control plane" – a self-hosted control & automation layer that turns:

* Spark + Iceberg + Trino + Airflow/Temporal + MLflow + OpenLineage + MinIO

into a **single platform** with:

* One workspace UI
* One jobs/workflows abstraction
* One metadata & lineage view
* One GitOps entrypoint

**Positioning:**

* **vs Databricks:** Own your control plane, no DBUs, portable stack; accept infra responsibility.
* **vs plain OSS:** Batteries-included and opinionated (reference architectures, templates, UI) instead of raw components.

---

## 4. Objectives & Non-Goals

### 4.1 Objectives

1. **Databricks Workflows parity (80/20)**

   * Create, schedule, and monitor multi-step jobs across Spark, dbt, Trino, and Python tasks.
2. **Unified lakehouse UX**

   * Single UI for jobs, data assets (Iceberg/Delta/Hudi tables), lineages, and metrics.
3. **GitOps-first**

   * Everything (jobs, environments, data contracts, lakehouse config) is declared in Git and synced to runtime.
4. **Vendor neutrality**

   * No dependency on Databricks APIs or licensing; all components OSS, pluggable, and self-hosted.
5. **Opinionated but pluggable stack**

   * Default stack: Airflow + Spark + Iceberg + Trino + MinIO + MLflow + OpenLineage + Prometheus/Grafana.
   * Allow swapping: Temporal/Prefect for orchestration, ClickHouse/Postgres for query, S3/GCS/ADLS for storage.

### 4.2 Non-Goals

* Managed SaaS; OLO is **self-hosted only**.
* Building a fully Databricks-compatible API façade for all endpoints.
* Replacing advanced, proprietary Unity Catalog features (fine-grained, cloud-native policy engines).
* Implementing a full IDE; notebooks/editing are delegated to JupyterHub/VS Code/other tools.

---

## 5. Target Users & Personas

1. **Data Platform Engineer**

   * Owns infra, k8s, storage, Spark clusters.
   * Needs a repeatable, secure, auditable platform to expose to data teams.
2. **Analytics Engineer**

   * Writes dbt models, SQL, occasionally Spark jobs.
   * Needs a simple way to define pipelines and monitor runs without learning k8s internals.
3. **Data Scientist / ML Engineer**

   * Trains models on Spark/Trino; uses MLflow.
   * Needs reproducible pipelines and experiment tracking.
4. **Data Product Owner**

   * Wants dashboards that show pipeline health, SLAs, and total platform cost.

---

## 6. Scope & Requirements

### 6.1 Functional Requirements

#### 6.1.1 Workspace & Projects

* F1. Create **workspaces** (logical tenants) that group:

  * Pipelines (jobs/workflows)
  * Lakehouse catalogs/schemas
  * ML projects
* F2. Each workspace is backed by:

  * A Git repo path (dbt, Spark jobs, config)
  * A storage prefix (e.g., `s3://lake/<workspace>/`)
  * One or more compute profiles (Spark cluster templates, Trino resource groups)

#### 6.1.2 Jobs & Workflows (Databricks Workflows Equivalent)

* F3. Define a **job** as a DAG of tasks (Spark, dbt, SQL, Python, shell, HTTP).
* F4. Tasks support:

  * SparkSubmit (Spark on k8s/standalone)
  * dbt run/test
  * Trino/ClickHouse SQL execution
  * Python scripts
  * Webhook steps
* F5. All jobs are defined in a **declarative YAML** spec (Git-first):

  * Schedules (cron, interval, triggered)
  * Dependencies
  * Retries/backoff
  * Resource profiles
* F6. Execute and monitor jobs via:

  * UI (trigger now, backfill)
  * API
  * CLI

#### 6.1.3 Lakehouse Layer

* F7. Support table formats:

  * Apache Iceberg (default)
  * Delta Lake and/or Hudi (via connectors)
* F8. Opinionated **medallion layers**:

  * bronze, silver, gold (and optional `ml`, `serving` layers)
* F9. Provide data catalog views:

  * Catalog → Schema → Tables
  * Table schema, statistics, partitions
  * Storage location and lifecycle (e.g., retention, compaction policies)

#### 6.1.4 Query Layer (SQL Warehouse Equivalent)

* F10. Integrate Trino as default distributed query engine (DuckDB/ClickHouse as options).
* F11. Provide:

  * Saved queries
  * Query history (per user, per workspace)
  * Basic result visualization (tables, trend lines)
* F12. Expose JDBC/ODBC endpoints for BI tools.

#### 6.1.5 Lineage & Observability

* F13. Instrument pipelines with OpenLineage:

  * Tasks emit dataset input/output events
  * Jobs + tasks → full lineage graphs
* F14. Integrate with Prometheus/Grafana:

  * Job success/failure rates
  * Latency, SLA status
  * Resource usage per workspace/job
* F15. Provide a **platform health dashboard** in UI.

#### 6.1.6 ML & Experiments

* F16. Provide baked-in MLflow server:

  * Experiment tracking
  * Model registry
* F17. Integrate MLflow runs with jobs:

  * Each pipeline step can log to MLflow
  * Link runs to job executions in UI.

#### 6.1.7 Governance & Access

* F18. Workspace-level roles:

  * Platform Admin, Workspace Admin, Data Engineer, Analyst, Viewer.
* F19. Pluggable auth (OIDC, SAML, LDAP).
* F20. Basic catalog-level permissions (schema/table access) configured via:

  * Trino access controls + underlying storage policies
  * Optional plug-in to Polaris/HMS for metadata governance.

---

### 6.2 Non-Functional Requirements

* N1. **Self-hosted** on Kubernetes (primary) or Docker Compose (dev).
* N2. Scale to:

  * 50–100 concurrent jobs
  * 20–50 concurrent interactive queries
* N3. Fully **GitOps**:

  * Workspace configs, jobs, cluster profiles, and lakehouse config are versioned.
* N4. High availability for control plane (multi-replica UI/API, persistent backing store).
* N5. Audit logs for all job and config changes.

---

### 6.3 Integrations

* **Storage:** MinIO (dev), S3/GCS/ADLS (prod).
* **Orchestrator:** Airflow (default), extensible to Temporal/Prefect.
* **Catalog:** Hive Metastore or Polaris; pluggable metadata backend.
* **Query engines:** Trino (primary), ClickHouse/Postgres/DuckDB (optional).
* **Notebooks:** JupyterHub/VS Code; registered in workspace config (links, not hosted by OLO).
* **CI/CD:** GitHub Actions/GitLab CI for:

  * Deploying updated configs/DAGs
  * Running tests (dbt, integration tests)

---

## 7. System Architecture

### 7.1 Logical Architecture

* **Control Plane (OLO Core)**

  * API server
  * Web UI (React or similar)
  * Config store (Postgres)
  * Auth & RBAC
  * Job registry & status service

* **Execution Plane**

  * Airflow/Temporal cluster
  * Spark cluster (k8s operator or standalone)
  * Trino cluster
  * MLflow server
  * OpenLineage collector
  * Prometheus/Grafana

* **Data Plane**

  * Object storage: MinIO/S3/GCS/ADLS
  * Table formats: Iceberg (default), Delta/Hudi connectors
  * Catalog: Hive Metastore/Polaris

### 7.2 Control Flow (Job Run)

1. User commits/update YAML job spec + dbt/Spark code to Git.
2. CI pipeline:

   * Validates config (schema, references, dry-run).
   * Applies changes via Helm/IaC or OLO API.
3. OLO Core updates job registry; instructs Airflow to sync DAGs.
4. Scheduler triggers run (on schedule or on demand).
5. Tasks:

   * Run Spark jobs (SparkSubmit to Spark cluster)
   * Run dbt models (dbt CLI)
   * Run Trino queries
     emitting OpenLineage events and Prometheus metrics.
6. OLO Core aggregates job status, logs, lineage.
7. UI presents job runs, logs, lineage, and metrics.

---

## 8. UX & Developer Experience

### 8.1 UI Concepts

* **Home:** Overall platform health, recent jobs, alerts.
* **Workspaces:** List of workspaces; each with:

  * Pipelines (jobs) tab
  * Data (tables, schemas) tab
  * Queries tab
  * ML Experiments tab
  * Settings (auth, resources, storage)
* **Job Detail View:**

  * DAG visualization
  * Task status, logs
  * Upstream/downstream dependencies
* **Lineage View:**

  * Dataset → jobs → datasets graph
  * Filter by workspace, schema, table.

### 8.2 Developer Workflow

1. **Design:** Define jobs and lakehouse layout in `spec/` + `pipelines/` directories.
2. **Implement:** Add dbt models, Spark scripts, SQL queries.
3. **Test locally:** Docker Compose stack (Spark + Trino + MinIO).
4. **Push to Git:** CI runs tests & validation.
5. **Deploy:** CI calls OLO API/Helm to apply changes.
6. **Monitor:** Use OLO UI for jobs and lineage; Grafana for metrics.

---

## 9. Security, Compliance & Governance

* TLS for UI/API and internal service comms.
* pluggable OIDC/SAML for SSO.
* Audit log of:

  * Job CRUD operations
  * Workspace changes
  * Role/permission changes
* Support for tagging datasets with:

  * Sensitivity (PII, Restricted, Public)
  * Retention policies
* Governance powered via:

  * Catalog (Polaris/HMS) for metadata
  * Trino access control for query enforcement
  * Storage-level policies (bucket policies, object ACLs).

---

## 10. Rollout Plan & Milestones

### Phase 0 – Design & Spec (You Are Here)

* Finalize PRD (this document).
* Define folder layout and Spec Kit structure.

### Phase 1 – Minimal Viable Platform (MVP)

* Docker Compose stack:

  * OLO Core (API + UI)
  * Airflow
  * Spark (master + worker)
  * MinIO
  * Trino
  * Postgres (Airflow + OLO config)
* Single workspace:

  * 1 sample pipeline (bronze → silver → gold using Spark/dbt)
  * Basic job creation via YAML
* Basic UI:

  * List jobs, runs, and logs

### Phase 2 – Lakehouse + Lineage Integration

* Introduce Iceberg + Hive Metastore/Polaris.
* Implement medallion conventions.
* Integrate OpenLineage:

  * Job → dataset graph in UI.

### Phase 3 – Query & ML

* Full Trino integration:

  * Saved queries, history
  * JDBC/ODBC connectivity
* Deploy MLflow and link runs to jobs.

### Phase 4 – k8s & GitOps

* Helm charts for all components.
* GitHub Actions pipeline:

  * Validate configs
  * Apply via Helm/OLO API
* Add role-based access control.

### Phase 5 – Hardening & Productionization

* HA deployment of control plane.
* RBAC + SSO integration.
* Production observability dashboards (Grafana, alerts).
* Documentation and runbooks.

---

## 11. Risks & Tradeoffs

* **Operational complexity:**
  Running full OSS stack (Spark, Trino, Airflow, Iceberg, MLflow) is heavier than Databricks managed services.
* **Talent requirements:**
  Requires in-house k8s/DevOps expertise; mitigated by opinionated Helm charts and runbooks.
* **Feature parity:**
  Some advanced Databricks features (AI Assistants, fine-grained Unity Catalog, Marketplace) will not be replicated; focus on 80/20 workflow and lakehouse needs.
* **Multi-cloud nuances:**
  Storage and networking differ per cloud; must keep provider-specific logic constrained to infra layer.

---

## 12. Success Metrics

* **Adoption**

  * # of teams/workspaces onboarded.
  * # of jobs migrated from Databricks/legacy schedulers.
* **Reliability**

  * Pipeline SLA adherence (% runs within SLO).
  * Job failure rate and MTTR.
* **Cost**

  * Total infra cost vs previous Databricks bill (target ≥ 50–80% reduction on compute/control-plane spend).
* **Platform Productivity**

  * Time from new project request → first production pipeline.
  * Time to add a new data source or table to lakehouse.

---

This PRD defines the improved, productized version of your self-hosted Databricks-equivalent stack: OpenLake Orchestrator – a GitOps-first, Databricks-style control plane for Spark + Iceberg + Trino workflows, entirely on open source and fully under your control.
