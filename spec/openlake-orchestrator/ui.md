# OpenLake Orchestrator Console — Production UI/UX Spec (Databricks-style)

## 0) Scope
This UI spec mirrors the Databricks Workspace console shell (left nav + global search + home onboarding cards) and maps each surface to the OpenLake Orchestrator stack.

Target: production-ready web console for:
- Workspaces (tenants/projects)
- Jobs & Pipelines (Airflow-backed orchestration)
- Catalog (Iceberg tables via Metastore/Trino)
- SQL (Trino "warehouse" + editor + history)
- Compute (Spark profiles + runtime status)
- Observability (run metrics, alerts, lineage pointers)
- ML (MLflow linkouts + run associations) [Phase 6]

## 1) Information Architecture (IA)

### Global (top bar)
- Global Search (typeahead): "Search data, tables, jobs, runs, recents…"
- Workspace selector (dropdown)
- Notifications (alerts, failed runs)
- User menu (profile, org, logout)

### Left Nav (primary)
Core:
- Home
- Workspace
- Recents

Data:
- Catalog
- Jobs & Pipelines
- Compute
- Marketplace (optional: template registry)

SQL:
- SQL Editor
- Queries
- Dashboards
- Alerts
- Query History
- SQL Warehouses

AI/ML:
- Playground (optional)
- Experiments (MLflow)
- Features (optional, later)
- Models (MLflow registry)
- Serving (optional, later)

Notes:
- Hide sections based on RBAC (e.g., Viewer sees Catalog/Queries/Dashboards only).
- "Marketplace" in OLO maps to "Templates" (pipeline templates + connectors), not a paid store.

## 2) Page Map (Routes)

Base: `/w/:workspaceSlug`

- `/w/:ws/home`
- `/w/:ws/workspace` (repo/notebooks integration links + file tree placeholder)
- `/w/:ws/recents`

Data:
- `/w/:ws/catalog`
- `/w/:ws/catalog/:catalog/:schema`
- `/w/:ws/catalog/:catalog/:schema/:table`
- `/w/:ws/jobs`
- `/w/:ws/jobs/:jobId`
- `/w/:ws/jobs/:jobId/runs/:runId`
- `/w/:ws/compute`
- `/w/:ws/compute/profiles/:profileId`
- `/w/:ws/templates` (optional)

SQL:
- `/w/:ws/sql/editor`
- `/w/:ws/sql/queries`
- `/w/:ws/sql/queries/:queryId`
- `/w/:ws/sql/dashboards`
- `/w/:ws/sql/dashboards/:dashboardId`
- `/w/:ws/sql/alerts`
- `/w/:ws/sql/history`
- `/w/:ws/sql/warehouses`

Lineage:
- `/w/:ws/lineage`
- `/w/:ws/lineage/datasets/:datasetKey`

Admin:
- `/w/:ws/settings`
- `/org/settings` (platform admin only)

## 3) Key Screens (Wireframe-Level Specs)

### 3.1 Home (Databricks-like onboarding)
Layout: two major blocks
1) Welcome banner (dismissible)
2) "Connect your data" cards
3) "Create your first pipeline" cards
4) "Take a course" / "Explore starter projects" (optional, can be docs links)

Components:
- `WelcomeBanner`
- `QuickActionsGrid`:
  - Upload data → opens "Ingestion Wizard" (Phase 5 optional)
  - Browse sample datasets → loads demo dataset to MinIO/Iceberg
  - Connect sources → connector templates (n8n / Airbyte later)
- `CreatePipelineCards`:
  - Learn about pipelines (docs link)
  - Explore sample pipeline (one-click deploy)
  - Build your own pipeline (opens Job YAML editor)

Production requirements:
- All cards must have deterministic fallbacks (no broken links).
- If workspace has no jobs/tables, show empty-state CTA instead of blank pages.

### 3.2 Catalog (Tables/Views browser)
Left: Catalog + Schema tree
Right: Table list or table detail

Table detail tabs:
- Overview (schema, location, stats)
- Partitions (if any)
- Sample Data (Trino query preview)
- History (Iceberg snapshots, if exposed)
- Lineage (graph snippet)
- Permissions (RBAC view-only unless admin)

Components:
- `CatalogTree`
- `TableList`
- `TableDetailHeader`
- `SchemaTable`
- `SampleQueryRunner`

### 3.3 Jobs & Pipelines
Jobs list:
- Filter chips: status, schedule, owner, tag
- Search
- "Create Job" button (RBAC-gated)
- Job cards/table with: name, schedule, last run, success rate

Job detail:
- Header: name, schedule, tags, owner
- Tabs: Overview | Runs | DAG | Settings
- Actions: Run Now, Pause/Resume schedule, Edit YAML

Run detail:
- Run timeline + task list
- Task status badges
- Logs viewer (stream or fetch)
- Artifacts: outputs, lineage events, MLflow links

Components:
- `JobsTable`
- `JobDetail`
- `DagGraph` (read-only visualization)
- `RunTimeline`
- `TaskLogsPanel`

### 3.4 SQL Editor / Warehouses
SQL Editor:
- Saved queries panel
- Query editor (Monaco)
- Results grid
- Explain plan (optional)
- "Run" uses selected warehouse (Trino resource group)

Warehouses:
- List of "SQL Warehouses" (maps to Trino clusters/resource groups)
- Status + basic config
- RBAC: view for Analyst; edit for Admin only

Components:
- `SqlEditor`
- `QueryResultsGrid`
- `WarehouseSelector`
- `WarehousesList`

### 3.5 Compute
Compute Profiles (Spark):
- list of profiles (driver/executor sizing)
- runtime health
- active jobs using profile
- "Create profile" (admin)

Components:
- `ComputeProfilesTable`
- `ProfileDetail`

### 3.6 Lineage
Dataset search → lineage graph
- Graph nodes: dataset, job, run
- Filters: time range, job, schema
- Click node → details (inputs/outputs, last updated)

Components:
- `LineageGraph`
- `DatasetDetail`

## 4) Layout System (Production-ready)

### 4.1 Grid + Shell
- App shell: fixed left nav (collapsible)
- Top bar fixed
- Content area scroll

Breakpoints:
- `sm` (≤640): nav becomes bottom sheet / hamburger
- `md` (641–1024): nav collapsible icons
- `lg` (≥1025): full nav

Spacing:
- base unit: 8px
- card padding: 16–24px
- page padding: 24px desktop, 12–16px mobile

### 4.2 Component Standards
- All pages must implement: Loading, Empty, Error states
- Error states include retry and diagnostic code
- Tables must support pagination and server-side filtering

## 5) Design Tokens (Minimal, production)
Typography:
- Primary: IBM Plex Sans (as per your preference)
- Monospace: IBM Plex Mono (SQL/editor)

Token set:
- `--font-sans: "IBM Plex Sans", system-ui, sans-serif;`
- `--font-mono: "IBM Plex Mono", ui-monospace, monospace;`
- `--radius-card: 12px;`
- `--shadow-card: 0 1px 2px rgba(0,0,0,.06), 0 4px 12px rgba(0,0,0,.06);`

Color:
- Use themeable tokens only (no hard-coded colors)
- Provide `light` and `dark` palettes via CSS variables

## 6) RBAC (UI enforcement)
Roles:
- PlatformAdmin
- WorkspaceAdmin
- Engineer
- Analyst
- Viewer

UI gates:
- Create/Edit Job: WorkspaceAdmin, Engineer
- Trigger Run: WorkspaceAdmin, Engineer (optional Analyst if policy allows)
- Edit Warehouses/Compute: PlatformAdmin, WorkspaceAdmin
- View Catalog/SQL: Analyst, Viewer, above

## 7) Telemetry + Observability (UI)
- Frontend performance: web vitals
- UI events: job run triggered, query executed, page load errors
- Correlate UI actions to run IDs and query IDs

## 8) Non-negotiable Production Requirements
- No blank pages: all views must have empty-state CTAs
- Deterministic navigation: links always resolve
- Server-side pagination for jobs/runs/tables/queries
- Log viewer must handle large logs (chunked fetch + virtualization)
- Accessibility: keyboard nav, focus states, aria labels
- Security: never render secrets; redact tokens in logs

## 9) Definition of Done (UI)
- Home, Catalog, Jobs, Run Detail, SQL Editor, Warehouses, Compute implemented
- All states: loading/empty/error
- RBAC gating works end-to-end
- Lighthouse: performance ≥ 80, accessibility ≥ 90 (baseline)
