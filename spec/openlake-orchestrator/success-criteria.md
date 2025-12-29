# Success Criteria (UPDATED: UI production readiness)

## UI Production Readiness
- No blank pages; all views have loading/empty/error states
- RBAC gating works end-to-end (UI + API)
- Logs viewer handles large logs (chunked + virtualized)
- Server-side pagination for jobs/runs/tables/queries
- Baseline accessibility: keyboard nav + aria labels on major components

## Platform Parity (80/20 Databricks)
- Jobs: DAG dependencies + schedules + run history + logs
- Catalog: browse Iceberg tables + schema + sample query preview
- SQL: editor + warehouses list + query history
- Compute: profiles visible + referenced by jobs

## Functional Parity Targets (80/20 Databricks)
- Jobs:
  - DAG-style task dependencies
  - Schedules + manual runs
  - Run history + status + logs
- Lakehouse:
  - Iceberg tables on S3-compatible store
  - Bronze/Silver/Gold conventions demonstrated
- SQL:
  - Trino query execution from UI/API
- Observability:
  - Prometheus metrics exposed and visible in Grafana
- Lineage:
  - OpenLineage events emitted and graph retrievable (Phase 6)

## Platform Quality Targets
- A first-time user can:
  - create a workspace
  - apply a job YAML
  - run it and view logs
  within 30 minutes on dev stack
- Control plane uptime (staging/prod):
  - 99.5%+ target (post-hardening)

## Performance Targets (Initial)
- Job run list loads < 2s for 1k runs
- Run detail loads < 2s for 100 tasks
- SQL query returns first page < 5s for typical gold table scans (small demo dataset)

## Cost & Portability Targets
- No proprietary Databricks dependencies
- All components deployable via Docker Compose and Helm
- Can move object store between MinIO/S3 without code changes (config-only)
