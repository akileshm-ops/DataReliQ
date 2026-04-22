# DataReliQ: Master Project Document (Executive Edition)

**Product Vision:** To be the definitive, Snowflake-native data observability and quality command center, providing institutional-grade reliability insights without ever reading business-row content.

---

## 1. Executive Summary
- **Product Name:** DataReliQ
- **Category:** Data Observability & Analytics (Snowflake Native App)
- **Tech Stack:** Streamlit + Snowpark (Python)
- **Core Value:** Real-time visibility into data freshness, SLA compliance, and pipeline health using metadata-only monitoring.

---

## 2. Product Architecture & Stack
### 2.1 Technical Stack
| Layer | Technology | Role |
| :--- | :--- | :--- |
| **Frontend** | Streamlit + Custom CSS | Native Snowflake UI / Dashboard |
| **Backend** | Snowpark Python | In-database compute & Business Logic |
| **Database** | Snowflake | Storage, Staging, and Metadata Source |
| **Orchestration** | Snowflake Tasks + SPs | Background scans & Report generation |
| **Visualization** | Plotly / Chart.js | Interactive analytics & Trend lines |

### 2.2 Schema & Data Model
- **`CONFIG`**: Stores user settings, SLA targets, and monitored object boundaries.
- **`STAGING`**: Holds computed metrics (Freshness, Pipeline health) to ensure sub-second dashboard performance.
- **`AUDIT`**: Canonical execution logs, notification history, and report tracking.

### 2.3 Architecture Pattern (MVC)
- **Views**: `views/` - Modular Streamlit components.
- **Controllers**: `controllers/` - Logic orchestrators connecting UI to Services.
- **Services**: `services/` - Core computation (Freshness formulas, SLA logic).
- **Repository**: `repository/` - Snowflake data access layer (queries/commands).
- **Infrastructure**: `infra/` - Connector management and session handling.

---

## 3. Core Modules & Functional Scope

### 3.1 Onboarding & Setup
- **Workflow**: Permission Grant -> Connection Test -> Scope Selection -> Metric Config.
- **Key Metric**: Setup Completion Score (Target: 100%).
- **Goal**: Zero-to-Dashboard in under 5 minutes.

### 3.2 Data Freshness
- **Features**: KPI Cards, Distribution Gauge, Trend Analysis, Stale Table Drill-down.
- **Formula**: `100 - ((hours_since_update - expected)/expected * 100)`.
- **Innovation**: Metadata-only (Account Usage + Info Schema) for near-zero performance impact.

### 3.3 SLA Compliance
- **Features**: Overall SLA Score, Component Cards (Freshness, Performance, Uptime).
- **Logic**: Weighted average of three core compliance metrics against user-defined targets.

### 3.4 Pipeline Monitor
- **Features**: Success Rate Trends, Error Category Analysis, Top-Failing Task ranking.
- **Source**: `ACCOUNT_USAGE.TASK_HISTORY` enriched with `QUERY_HISTORY`.

### 3.5 Execution Logs & Reports
- **Logs**: Chronological audit trail of all background activities.
- **Reports**: Automated PDF/CSV artifacts for stakeholders, supporting ad-hoc and scheduled triggers.

---

## 4. Development Workflow & Standards

### 4.1 Coding Standards
- **TS (Strict) / Python**: Typing is mandatory.
- **Naming**: PascalCase for Classes, snake_case for functions/vars.
- **Documentation**: Google-style docstrings for all methods.
- **Performance**: Optimize Snowpark queries for cost and speed.

### 4.2 TDD & Validation Protocol
- **Validation**: Never assume correctness. Every metric must be traceable to a source query.
- **Test Cases**: Create mock Snowflake metadata for edge-case testing (e.g., extremely large tables, failing tasks).

### 4.3 UI/UX Aesthetics
- **Theme**: "Elegant Dark" / "Clean Enterprise" (Navy/Teal/Slate).
- **Micro-animations**: Use Streamlit's native transitions + custom CSS for premium feel.
- **Gating**: Setup must be complete before Dashboard access is granted.

---

## 5. Roadmap & Strategic Enhancements (90-Day Plan)

### Phase 1: Foundation (Weeks 1-2)
- [ ] Standardize `AUDIT.RUN_LOG` across all modules.
- [ ] Implement robust error taxonomy for fail-safe diagnostics.
- [ ] Finalize the "Setup to Staging" persistence layer.

### Phase 2: Intelligence (Weeks 3-5)
- [ ] Integrate Snowflake AI (Cortex) for anomaly detection in Freshness trends.
- [ ] Develop the "AI Insights" side panel for automated RCA (Root Cause Analysis).
- [ ] Refine the "Stale Table" trend visualization with predictive alerts.

### Phase 3: Enterprise Readiness (Weeks 6-12)
- [ ] Production-grade PDF Report generation (FPDF2).
- [ ] Advanced Notification routing (Slack/Email/Webhooks).
- [ ] Multi-role permission model (Viewer vs. Admin).

---

## 6. Risk & Mitigation Matrix

| Risk | Impact | Mitigation Strategy |
| :--- | :--- | :--- |
| **Metadata Latency** | Dashboard stale by ~2hrs | Hybrid approach: combine Account Usage with INFORMATION_SCHEMA. |
| **Compute Cost** | High Snowpark credits | Use serverless tasks with strict timeouts; stage metrics once. |
| **Data Privacy** | Regulatory concerns | Zero-read policy on business rows; monitor only system metadata. |
| **UI Complexity** | User friction | Guided Wizard (Step-by-step onboarding) with "Skip" for advanced users. |

---

## 7. Success KPIs
1. **Time-to-Value**: < 5 mins for first dashboard render.
2. **System Health**: 99.9% success rate for background scans.
3. **Accuracy**: 100% parity between Dashboard KPIs and Snowflake metadata.
4. **Adoption**: > 80% setup completion rate for trial users.
