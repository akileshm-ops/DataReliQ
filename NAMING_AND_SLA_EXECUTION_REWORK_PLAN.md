# DataReliQ Content and Live-Run UX Spec (Finalized Decisions)

Last updated: 2026-04-22  
Applies to: `DataReliQ v17.html` and Streamlit/Snowpark production build

## 1) Confirmed Product Decisions

These are finalized from your review:

1. Keep module name as `SLA`.
2. Merge `Stale Tables` under `Freshness` everywhere.
3. Use recency label format: `Data as of HH:MM UTC`.
4. SLA popups use rule/template-driven insights from metrics.
5. Execution-log accordion uses fixed core KPIs + expandable extras.
6. Show `Run ID` in both logs table and accordion.

## 2) Global UX and Naming Standards

### 2.1 Naming conventions

- Keep simple, consistent module names:
  - `Data Freshness`
  - `SLA`
  - `Pipeline Monitor`
  - `Execution Logs`
  - `Reports`
  - `Settings`
- Use Title Case for page titles and KPI titles.
- Use consistent status terms only: `Running`, `Success`, `Warning`, `Failed`.

### 2.2 Recency language

- Aggregated modules: show one recency line in page header area:
  - `Data as of 09:00 UTC`
- SLA cards: no `Last Evaluated` text on each card.
- Execution Logs accordion (run-level): use `Started At`, `Completed At`, and run-specific timestamps.

### 2.3 Actual vs target clarity

Every SLA KPI and related popup must show:
- `Actual`
- `Target`
- `Variance` (Actual - Target)

## 3) Module-Wise Naming, Description, and KPI Copy

## 3.1 Data Freshness

### Page subtitle
`Aggregated freshness health for the selected time range and monitored scope.`

### KPI cards (aggregated)
- `Freshness Score`
- `Tables Monitored`
- `Tables At Risk`
- `Most Stale Table`

### Card helper copy pattern
- `Freshness Score`: `Composite freshness score across monitored tables (0-100).`
- `Tables At Risk`: `Tables currently in warning or critical freshness state.`
- `Most Stale Table`: `Table with highest staleness in selected range.`

### Value proof language
- `See where freshness is degrading before downstream SLAs are impacted.`

## 3.2 SLA

### Page subtitle
`Aggregated SLA performance for the selected time range across freshness, latency, and uptime.`

### KPI cards (aggregated)
- `Overall SLA Compliance`
- `Freshness SLA`
- `Performance / Latency SLA`
- `Uptime SLA`

### SLA card secondary lines (standardized)
- `Target: 99.5% | Variance: -0.3 pp`
- `Breaches in range: 4`
- `Data as of 08:45 UTC`

### Value proof language
- `Translate technical reliability into clear target attainment and breach risk.`

## 3.3 Pipeline Monitor

### Page subtitle
`Aggregated pipeline reliability, failures, and runtime behavior for the selected time range.`

### KPI cards (aggregated)
- `Pipeline Health`
- `Active Pipelines`
- `Failed Tasks (24H)`
- `Avg Runtime`
- `Target Runtime`

### Value proof language
- `Identify runtime drift and failure concentration before incidents scale.`

## 3.4 Execution Logs

### Page subtitle
`Run-by-run operational history with module-specific live snapshots and diagnostics.`

### Logs table columns (updated)
- `Run Time`
- `Run ID`
- `Module`
- `Run Type`
- `Action`
- `Status`
- `Summary`

### Value proof language
- `Audit every run with traceable status, metrics snapshot, and drill-down diagnostics.`

## 3.5 Reports

### Page subtitle
`Generate and track report outputs by module and selected date range.`

## 3.6 Settings

### Page subtitle
`Manage monitored scope, thresholds, schedules, notifications, and integrations.`

## 4) SLA Popup Spec (Rule/Template Driven)

## 4.1 Popup structure

1. Title: `<SLA Card Name> - Insights`
2. Chips row (3 chips):
  - Primary metric chip
  - Target chip
  - Time-window chip
3. Insight bullets (5 bullets):
  - Current state
  - Top contributor
  - Pattern detection
  - Risk/impact statement
  - Recommended action
4. Footer action: `Done`

## 4.2 Chip templates by card

### Overall SLA Compliance
- `Actual: {overall_actual_pct}%`
- `Target: {overall_target_pct}%`
- `{window_label}`

### Freshness SLA
- `Avg staleness: {avg_staleness_human}`
- `Target: {freshness_target_human}`
- `{window_label}`

### Performance / Latency SLA
- `Avg runtime/query: {latency_actual_human}`
- `Target: {latency_target_human}`
- `{window_label}`

### Uptime SLA
- `Actual: {uptime_actual_pct}%`
- `Target: {uptime_target_pct}%`
- `{window_label}`

## 4.3 Insight bullet templates (rule-driven)

Bullet 1 (state):
- `Current {metric_name} is {actual} versus target {target} ({variance_pp} pp).`

Bullet 2 (driver):
- `Top contributor: {top_schema_or_task} accounted for {contribution_pct}% of variance.`

Bullet 3 (pattern):
- `Pattern detected: {pattern_summary} within {time_pattern_window}.`

Bullet 4 (risk):
- `Risk: {breach_count} breach events in range; projected impact is {impact_summary}.`

Bullet 5 (action):
- `Action: {recommended_action} to recover {estimated_gain}.`

## 4.4 Feasibility notes

- Fully feasible with existing staging model.
- Needs consistent availability of:
  - `actual_pct`, `target_pct`, `variance_pp`, `breach_count`,
  - `top_contributor`, `pattern_summary`, `recommended_action`.
- If contributor/pattern data is missing, fallback to deterministic text:
  - `No dominant contributor detected in selected range.`

## 5) Execution Logs + Accordion Spec (Run-Scoped)

## 5.1 Run ID standard

- Display format: `RUN-YYYYMMDD-HHMMSS-<short_hash>`
- Must be unique and stable across UI, logs table, and diagnostics exports.

## 5.2 Accordion layout

### Header strip
- `Run ID`
- `Module`
- `Status`
- `Run Type` (`Scheduled` / `Manual`)
- `Started At`
- `Completed At`
- `Duration`

### Body section A: Module Current Live Status (fixed core cards)
- Exactly 6 core KPI tiles.

### Body section B: Expandable Extras
- Retry and orchestration details.
- Top contributors/drivers.
- Error summary and IDs.
- Scope summary and object counts.

### Body section C: Footer note
- `Data reflects current dashboard state for the <module> module.`

## 5.3 Fixed core KPI sets by module

### Freshness run (core 6)
- `Freshness Score`
- `Tables Monitored`
- `Tables At Risk`
- `Most Stale Table`
- `Avg Staleness`
- `Data as of HH:MM UTC`

### SLA run (core 6)
- `Overall SLA`
- `Active SLAs`
- `Breaches (30D)`
- `At Risk`
- `Most Affected`
- `Data as of HH:MM UTC`

### Pipeline run (core 6)
- `Pipeline Health`
- `Active Pipelines`
- `Failed Tasks (24H)`
- `Avg Runtime`
- `Target Runtime`
- `Data as of HH:MM UTC`

## 5.4 Expandable extras by module

### Freshness extras
- `Critical Tables`
- `Top 3 stale tables`
- `Delta vs previous run`
- `Objects scanned`
- `Rows processed`

### SLA extras
- `Freshness SLA actual/target`
- `Latency SLA actual/target`
- `Uptime SLA actual/target`
- `Variance breakdown by component`
- `Top breached objects`

### Pipeline extras
- `Top failing tasks`
- `Error category breakdown`
- `Longest runtime task`
- `Retry count`
- `Queue wait / execution split` (if available)

## 6) Merged Stale-Tables Decision Impact

Since `Stale Tables` is merged into `Freshness`:
- Remove standalone `Stale Tables` module tags from logs filters and rows.
- Convert stale-detection actions to `Freshness` action names, e.g.:
  - `Freshness Stale Detection`
- Keep stale-related KPIs in Freshness cards and accordion extras.

## 7) Value-Proving View (Business Framing)

## 7.1 What this improves for buyers/users

- Clearer naming reduces onboarding friction.
- SLA cards become decision-ready (actual vs target vs variance).
- Run-level accordions close trust gap between aggregate dashboards and latest operations.
- Unified stale/freshness story reduces module confusion.

## 7.2 What this improves for delivery teams

- Standardized copy system lowers future UI drift.
- Template-driven insights are deterministic and auditable.
- Fixed-core + extras accordion pattern is reusable across modules.
- Run ID visibility improves support, audit, and incident triage.

## 8) Feasibility and Delivery Check

## 8.1 Feasibility summary

- Naming/copy updates: low risk, immediate.
- SLA popup templating: low-medium risk (needs structured payload fields).
- Execution log table + accordion upgrade: medium risk (run_id wiring + per-module snapshot contract).
- Stale merge into Freshness: low-medium risk (UI filter/tag cleanup + mapping updates).

## 8.2 Data contract minimum for release

- Run envelope:
  - `run_id`, `module`, `run_type`, `status`, `started_at`, `completed_at`, `duration_sec`
- Snapshot envelope:
  - `module_core_kpis` (fixed 6)
  - `module_extra_kpis` (object/list)
  - `data_as_of_utc`
- SLA popup envelope:
  - `actual`, `target`, `variance`, `window_label`, `breach_count`, `driver`, `pattern`, `action`

## 9) Implementation Sequence (Execution Ready)

1. Update labels, subtitles, status wording, and stale->freshness merge in UI copy.
2. Add `Run ID` column and accordion header metadata.
3. Wire fixed 6 KPI tiles per module in accordion.
4. Add expandable extras section.
5. Replace static SLA modal text array with template-driven generator using live metrics payload.
6. Add fallback text paths for missing optional data.

## 10) Review Gates Before Final Freeze

- Copy review: naming consistency and concise descriptions.
- Product review: module narratives and value claims.
- Engineering review: payload contract completeness.
- QA review: aggregate pages vs run-level accordion separation.
- Stakeholder review: SLA popup usefulness and actionability.
