# UAT – User Acceptance Testing (UPDATED: UI parity)

## Goal
Validate that OpenLake Orchestrator provides Databricks-like workflow automation (jobs/runs/dependencies), a lakehouse (Iceberg), SQL querying (Trino), and production-grade console UI.

## Personas
- Data Platform Engineer
- Analytics Engineer
- Data Scientist / ML Engineer
- Product Owner (read-only verification)

## Preconditions
- Dev stack up (docker compose) OR staging (helm) is deployed
- A workspace exists: `analytics`
- A sample job exists: `customer_daily` (bronze→silver→gold)
- Trino catalog is configured and reachable
- OpenLineage collector is reachable (if Phase 6 tests are run)

## Test Scenarios

### Core (Must Pass)
| ID | Scenario | Steps | Expected |
|----|----------|-------|----------|
| U-01 | Create Workspace | API/UI create workspace | Workspace appears in list and detail view |
| U-02 | Apply Job YAML | Register job spec | Job appears; validation errors are explicit |
| U-03 | Trigger Run | Click "Run now" or API trigger | Run created; tasks progress; final state recorded |
| U-04 | View Logs | Open run → task logs | Logs visible per task; includes timestamps |
| U-05 | SQL Query | Execute SQL via UI/API | Query returns results; errors are readable |
| U-06 | Sample Pipeline Output | Inspect gold table | Gold table exists and returns rows |

### Core UI Scenarios (Must Pass)
| ID | Scenario | Expected |
|----|----------|----------|
| U-UI-01 | Shell renders | Left nav + top bar + workspace switcher visible |
| U-UI-02 | Home onboarding | Cards render; CTAs are functional or safely stubbed |
| U-UI-03 | Catalog browse | Tree/list/detail work; sample query runs |
| U-UI-04 | Jobs list | Filters/search/pagination work |
| U-UI-05 | Run detail | Timeline + task statuses + logs viewer work |
| U-UI-06 | SQL editor | Query executes; results render; history captured |
| U-UI-07 | Empty states | No page renders blank; all have CTA |
| U-UI-08 | Error states | Failures show readable error + retry |

### Extended (Should Pass by Phase)
| ID | Scenario | Expected |
|----|----------|----------|
| U-07 | Lineage Graph | Dataset/job lineage visible and consistent |
| U-08 | MLflow Linkage | Job run links to MLflow run/experiment |
| U-09 | RBAC | Unauthorized user cannot view/trigger runs |
| U-10 | Helm Deploy | Staging deploy healthy; readiness checks pass |

## Exit Criteria
- All "Core (Must Pass)" scenarios pass in at least one environment (dev or staging)
- All U-UI-* pass in dev or staging
- No P0 issues remain (crash, data loss, cannot run jobs, cannot query)
- No P0 issues: crashes, broken navigation, blank screens, inaccessible critical actions
