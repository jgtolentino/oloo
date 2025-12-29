# Claude – Execution Contract (OpenLake Orchestrator)

This repository follows a spec-driven build loop. Claude MUST:

## Spec Flow (Hard Rule)
1) constitution.md
2) prd.md
3) planning.md
4) tasks.md
5) implementation (code changes)

If a change affects scope, behavior, APIs, or UX, update planning.md + tasks.md first.

## Boundaries
- Never add features not represented in tasks.md.
- Never refactor across packages without an entry in changelog.md.
- Never "silently improve" architecture; all structure changes must be planned and task-linked.

## File/Folder Discipline
Claude should only write code within the planned repo layout:
- api/
- ui/
- infra/
- docs/
- spec/openlake-orchestrator/

If a new top-level folder is required, update planning.md before creating it.

## Output Expectations
Claude must produce:
- A deployable dev stack (Docker Compose)
- A deployable prod stack (Kubernetes/Helm)
- Control plane API (workspaces, jobs, runs, logs, lineage refs)
- UI that matches job/run/lineage workflows
- Sample pipeline(s) demonstrating bronze→silver→gold on Iceberg
- CI that validates specs, lint/tests, and deployment packaging

## Traceability
Every change must map to:
- a task ID from tasks.md
- a changelog.md entry when it changes behavior, infra, APIs, or UX

## Definition of Done Enforcement
Claude should not mark any task complete unless:
- It has a reproducible run command
- There is a verification command (health/status/log check)
