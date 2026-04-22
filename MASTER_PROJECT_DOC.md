# DataReliQ Master Project Document

Last updated: 2026-04-22  
Prepared from: `DataReliQ v17.html`, `DataReliQ_v17 UI logic.html`, `tools_rules_workflow 1.md`, `app_logs 1.md`, `version_changes 1 (1).md`

## 1) Product Definition

**Product name:** DataReliQ  
**Category:** Snowflake-native data observability, analytics, and monitoring application  
**Runtime stack:** Streamlit + Snowpark (Snowflake Native App context)

DataReliQ is designed to help Snowflake users monitor data freshness, SLA compliance, pipeline health, execution reliability, and reporting outcomes without reading business table content directly. The product emphasizes metadata-driven monitoring from Snowflake system views plus app-owned config/staging/audit tables.

## 2) Core Value Proposition

DataReliQ provides a single control plane for:
- Data freshness visibility and stale table risk
- SLA posture and trend tracking
- Pipeline execution health and failure diagnostics
- Operational execution logs
- Configurable reports and alert workflows

Differentiators from current materials:
- Snowflake-first experience and permissions workflow
- Metadata-only monitoring posture (no direct business data reads)
- Setup-to-dashboard guided onboarding with persisted app state
- Unified threshold/config system affecting KPIs, alerts, and reports

## 3) Target Users

- Snowflake platform owners
- Data engineering leads
- Analytics engineering teams
- Data governance / reliability stakeholders
- Operations teams managing task health and SLA commitments

## 4) High-Level Architecture

Documented architecture pattern is MVC-style:
- `views/` for Streamlit UI pages/components
- `controllers/` for request orchestration
- `services/` for business rules and aggregation
- `repository/` for Snowflake data access
- `infra/` for connector/session concerns

Core platform components:
- Streamlit front-end shell and multi-section dashboard
- Snowpark Python for in-Snowflake computation and data operations
- Snowflake Tasks + Stored Procedures for scheduled/triggered refreshes
- Config/Staging/Audit schema model for operational persistence

## 5) Data Domains and Snowflake Sources

### 5.1 External/system metadata sources

- `SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY`
- `SNOWFLAKE.ACCOUNT_USAGE.TASK_HISTORY`
- `SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSE_METERING_HISTORY`
- `SNOWFLAKE.ACCOUNT_USAGE.TABLE_STORAGE_METRICS`
- `SNOWFLAKE.ACCOUNT_USAGE.ACCESS_HISTORY`
- `SNOWFLAKE.ACCOUNT_USAGE.USAGE_IN_CURRENCY_DAILY`
- `INFORMATION_SCHEMA.TABLES` (used for lower-latency table metadata checks)
- `SHOW DATABASES / SCHEMAS / WAREHOUSES / GRANTS` for setup and validation

### 5.2 App-owned schemas (logical model inferred from docs)

**CONFIG**
- `CONFIG.SETTINGS`
- `CONFIG.SETUP_STATE`
- `CONFIG.MONITORED_OBJECTS`
- `CONFIG.MODULE_METRICS`
- `CONFIG.ALERT_RULES`
- `CONFIG.NOTIFICATION_RECIPIENTS`
- `CONFIG.REPORT_SCHEDULES` (future-ready in UI logic doc)

**STAGING**
- `STAGING.FRESHNESS_METRICS`
- `STAGING.FRESHNESS_SCORES`
- `STAGING.STALE_TABLE_HISTORY`
- `STAGING.SLA_COMPLIANCE`
- `STAGING.PIPELINE_HEALTH`
- `STAGING.PIPELINE_FAILURES`
- `STAGING.REFRESH_LOG` (from app logs doc)

**AUDIT**
- `AUDIT.RUN_LOG`
- `AUDIT.NOTIFICATION_LOG`
- `AUDIT.REPORT_RUNS`

## 6) Product Workflow Lifecycle

1. User opens app and enters Setup flow if not complete.
2. User configures permissions/scope/benchmarks/settings.
3. App persists config in `CONFIG.*`.
4. Manual sync or schedule triggers compute refresh workflows.
5. Compute reads Snowflake metadata, applies thresholds/formulas, writes `STAGING.*`.
6. UI modules read staging outputs and render KPIs/charts/tables.
7. Logs and audit trails are written to `STAGING.REFRESH_LOG` and/or `AUDIT.RUN_LOG`.
8. Notifications and reports are generated from staged results and configuration.

## 7) Module-by-Module Functional Scope

### 7.1 Setup

Includes:
- App shell choice and setup gating
- Permission grant guidance and SQL generation
- Object scope selectors (warehouse/database/schema/table)
- Connection tests
- Module metric initialization
- Final confirmation into dashboard

Representative setup formulas (from UI logic artifact):
- `grant_coverage_pct = granted_required_privileges / total_required_privileges * 100`
- `monitored_table_count = explicit_selected_tables + inherited_tables_from_selected_scopes`
- `connection_test_score = passed_checks / total_checks * 100`
- `setup_completion_pct = completed_required_sections / total_required_sections * 100`

### 7.2 Global Dashboard Controls

- Sidebar navigation
- Shared date window controls
- Manual sync trigger
- Notification center access
- Export actions
- Quick/targeted ad-hoc scan workflow

### 7.3 Freshness

Includes:
- Freshness KPI cards
- Distribution gauge
- Trend line
- Table-level at-risk grid
- Stale table trend analysis

Core formulas captured in project artifacts:
- `table_freshness_score = max(0, min(100, 100 - ((hours_since_update - expected_refresh_hours)/expected_refresh_hours)*100))`
- `overall_freshness = avg(table_freshness_score)`
- `tables_at_risk = count(score < warning_threshold)`
- `critical_tables = count(score < critical_threshold or hours_since_update > max_staleness_hours)`

### 7.4 SLA

Includes:
- Overall SLA KPI
- Component cards (freshness/performance/uptime)
- SLA trend chart
- SLA insights modal with drill guidance

Core formulas:
- `overall_sla_pct = weighted_avg(freshness_compliance_pct, performance_compliance_pct, uptime_compliance_pct)`
- `freshness_compliance_pct = fresh_tables_within_sla / total_monitored_tables * 100`
- `latency_compliance_pct = runs_within_target / total_runs * 100`
- `uptime_pct = successful_pipeline_intervals / total_pipeline_intervals * 100`

### 7.5 Pipeline Monitor

Includes:
- Task success trend
- Failure category analysis
- Top failing tasks table

Primary source:
- `ACCOUNT_USAGE.TASK_HISTORY` (+ optional `QUERY_HISTORY` enrichment)

### 7.6 Execution Logs

Includes:
- Run/event table with status filters
- Row-level detail expansion for diagnostics
- Module-linked run context

Noted naming inconsistency to resolve:
- `STAGING.REFRESH_LOG` in one document
- `AUDIT.RUN_LOG` in UI logic mapping  
Recommendation: choose one canonical run-log table and standardize repository/API usage.

### 7.7 Reports

Includes:
- Report listing and downloadable artifacts
- Ad-hoc report request form (module/date range/format/recipients)
- Future schedule support pattern

### 7.8 Settings

Includes:
- Scan cadence and scheduler config
- AI/provider settings
- Connection/object scope edits
- Module threshold edits
- Notification recipients and alert routing

## 8) Current Implementation Status (as reflected in changelog + v17 artifacts)

Completed/advanced:
- Multi-section dashboard shell with setup and settings flows
- Freshness, SLA, pipeline, execution logs, and reports sections
- Config-driven module editing UX
- Auto-scan and execution-log support added in recent commits
- Improved onboarding/grant instructions and UI refinements

Likely still in progress/needs hardening:
- Full backend persistence parity for all mock UI controls
- End-to-end production report job orchestration
- Strong canonical data contract between UI, controller/service layer, and Snowflake tables
- Formalized error taxonomy and reliability SLOs for the app itself

## 9) Security and Data Access Posture

Design intent from current project material:
- Read metadata about workloads/tables, not business-row content
- Use scoped Snowflake privileges
- Support explicit monitored-object boundaries
- Capture operational logs for observability and auditability

## 10) Key Risks and Mitigations

1. **ACCOUNT_USAGE latency risk**  
   Mitigation: combine with `INFORMATION_SCHEMA` for near-real-time freshness checks and clearly label latency windows in UI.

2. **Config-to-KPI drift risk**  
   Mitigation: central resolver for canonical settings and versioned metric contracts.

3. **Log model inconsistency (`REFRESH_LOG` vs `RUN_LOG`)**  
   Mitigation: adopt a unified log schema and deprecate aliases.

4. **Mock/real behavior divergence**  
   Mitigation: replace simulated values with repository-driven data loaders module-by-module.

5. **Permission complexity in Native App installs**  
   Mitigation: guided grant wizard + validation checks + actionable failure messages.

## 11) Recommended Canonical Contracts

- Single source of truth for thresholds: `CONFIG.MODULE_METRICS` + resolver into `CONFIG.SETTINGS`
- Single source of truth for monitored scope: `CONFIG.MONITORED_OBJECTS`
- Single orchestration entrypoint for refresh (manual + scheduled)
- Single audit envelope for every run:
  - `run_id`, `trigger_type`, `module_scope`, `started_at`, `completed_at`, `status`, `error_message`, `rows_processed`

## 12) 90-Day Execution Plan

### Phase 1 (Foundation)
- Finalize schema contracts and table DDLs
- Standardize run-log model and repository interfaces
- Wire setup/settings persistence end-to-end

### Phase 2 (Operationalization)
- Productionize freshness/SLA/pipeline computations in Snowpark + procedures
- Task scheduling + retry/failure handling
- Replace UI mock values with live staging queries

### Phase 3 (Reliability + Go-to-market readiness)
- Reports job framework with status tracking and artifact retention
- Alert routing polish and notification rules
- Observability of the app itself (run success rate, processing latency, error rates)
- Security/permissions validation checklist for customer onboarding

## 13) Definition of Success (v1)

- Setup completion rate > 80% for first-time installs
- Scheduled refresh success rate >= 99%
- Median dashboard load time < 3s for cached/staged views
- Freshness and SLA KPIs traceable to documented formulas and source tables
- Zero direct business-data reads; metadata-only posture maintained

## 14) Master Summary

DataReliQ is already well-scoped as a Snowflake-native observability product with strong module design. The fastest path to production maturity is to lock canonical schema/contracts, unify logging, and systematically replace mock UI logic with Snowpark-backed staged metrics while preserving the current onboarding and settings UX strengths.

## 15) UX Rework Status (Locked Decisions)

Finalized UX/content decisions are documented in:
- `NAMING_AND_SLA_EXECUTION_REWORK_PLAN.md`

Locked items:
- Keep `SLA` naming.
- Merge `Stale Tables` into `Freshness` everywhere.
- Use recency label `Data as of HH:MM UTC`.
- Use rule/template-driven SLA popup insights (not static narrative).
- Execution Logs accordion uses fixed core KPIs plus expandable extras.
- `Run ID` displayed in logs table and accordion.
