# DataReliQ: Tools, Rules, and Workflow Documentation

## Overview
DataReliQ is a Snowflake Native App built with Streamlit that provides data quality monitoring and AI readiness scoring. This document outlines the tools used, development rules, and application workflow.

## Tools and Technologies

### Core Framework
- **Streamlit**: Web framework for building the interactive dashboard
- **Python 3.8+**: Programming language with Snowpark runtime

### Data Processing and Visualization
- **Pandas**: Data manipulation and analysis
- **Plotly**: Interactive charts and visualizations
- **FPDF2**: PDF generation for reports

### Snowflake Integration
- **Snowflake-Snowpark-Python**: Python API for Snowflake operations
- **Snowflake-Native-Apps-Permission-Stub**: Permission handling for Native Apps
- **Snowflake-Native-Apps-Permission**: Production permission management

### Development Environment
- **Conda**: Environment management via environment.yml
- **Git**: Version control
- **VS Code**: Recommended IDE

## Project Structure and Architecture

### MVC Pattern Implementation
- **Views**: Streamlit UI components (`views/` directory)
- **Controllers**: Business logic handlers (`controllers/` directory)
- **Services**: Application services (`services/` directory)
- **Repositories**: Data access layer (`repository/` directory)
- **Infrastructure**: Core connectors (`infra/` directory)

### Key Components
- **SnowflakeConnector**: Manages database connections
- **Session State Management**: Streamlit session for state persistence
- **Background Tasks**: Snowflake Tasks for automated scanning

## Development Rules and Best Practices

### Code Organization
- Use clear separation of concerns (MVC pattern)
- Maintain consistent naming conventions
- Include docstrings for all classes and methods
- Handle exceptions appropriately with user-friendly messages

### Database Design
- Use CONFIG schema for user settings and configuration
- Use STAGING schema for computed results and logs
- Implement proper access controls and grants
- Follow Snowflake Native App security best practices

### UI/UX Guidelines
- Use wide layout with collapsible sidebar
- Implement consistent styling and theming
- Provide clear navigation and user feedback
- Ensure responsive design for different screen sizes

## Application Workflow

### User Journey
1. **Onboarding**: First-time users accept consent and configure initial settings
2. **Configuration**: Set up SLA benchmarks, scope selection, and notification preferences
3. **Monitoring**: View data freshness, SLA compliance, stale tables, and pipeline health
4. **Operations**: Monitor execution logs and system performance

### Data Flow
```
User Action → Controller → Service → Repository → Snowflake
                                      ↓
UI Render ← Controller ← Service ← Repository ← Results
```

### Background Processing
- **Manual Trigger**: User clicks "Sync Now" button
- **Scheduled Tasks**: Automated scans via Snowflake Tasks
- **Stored Procedures**: Execute core business logic in Snowflake
- **Log Tracking**: Record execution history in STAGING.REFRESH_LOG

### Key Workflows

#### 1. Data Quality Scanning
- Reads from ACCOUNT_USAGE views (TABLE_STORAGE_HISTORY, TASK_HISTORY, QUERY_HISTORY)
- Computes freshness scores, SLA compliance, and pipeline health
- Stores results in STAGING tables
- Updates execution logs

#### 2. Settings Management
- User configures benchmarks and scope
- Settings stored in CONFIG tables
- Triggers background task updates
- Affects subsequent scans

#### 3. Dashboard Rendering
- Reads from STAGING tables
- Applies date filtering and aggregations
- Displays KPIs and visualizations
- Provides drill-down capabilities

### Task Integration
- Uses serverless Snowflake Tasks for background processing
- Supports configurable schedules (minutes, hours, days)
- Can be suspended/resumed dynamically
- Follows Native App best practices for compute management

## Deployment and Configuration

### Environment Setup
- Use provided environment.yml for conda environment
- Configure Snowflake connection parameters
- Set up proper permissions via SQL scripts

### Native App Packaging
- Includes manifest.yml for app definition
- Contains setup.sql for database initialization
- Provides permission grants for secure operation

## Monitoring and Maintenance

### Health Checks
- Execution logs track system performance
- Error handling with detailed messages
- Status monitoring for background tasks

### Data Freshness
- Automated scoring based on table update patterns
- SLA compliance tracking
- Risk assessment for stale data

This architecture ensures scalable, secure, and maintainable data quality monitoring within the Snowflake ecosystem.</content>
<parameter name="filePath">/home/aximsoft/Documents/datareliq/Data-Reliq/tools_rules_workflow.md