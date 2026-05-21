# Network Performance Analytics

AI-powered telecom network performance intelligence for uptime optimization, latency monitoring, and SLA compliance.

## Overview

This Native App provides a comprehensive Network Performance Analytics dashboard covering:

- **Executive Summary** — Overall uptime, latency, packet loss, MTTR, outages, throughput with MoM deltas
- **Network Uptime** — Tower-level uptime grades, regional heatmaps, top/bottom performers
- **SLA Compliance** — Compliance rates by region and tier, failure breakdowns
- **Capacity Planning** — Utilization monitoring, forecast demand vs capacity, critical towers
- **Latency & Packet Loss** — Avg/P95/P99 latency, jitter, packet loss grades, throughput
- **Outage Analysis** — Root cause breakdown, severity, MTTR/MTTD by region and repair type
- **Vendor Performance** — Equipment comparisons, latency vs throughput scatter analysis
- **Trends** — Latency and MTTR time-series with rolling averages, regional comparisons
- **ML Predictions** — Snowflake ML forecasting, anomaly detection, network health scores

## Installation

After installing, configure the app by binding your data tables to the required references. Each reference expects a table (or view) with the columns described in the reference configuration.

## Required Tables

| Reference | Key Columns |
|-----------|-------------|
| NETWORK_UPTIME_TABLE | SNAPSHOT_DATE, TOWER_NAME, TOWER_TYPE, REGION_NAME, UPTIME_PCT_30D, DOWNTIME_MINUTES, UPTIME_GRADE |
| REGIONAL_UPTIME_TABLE | SNAPSHOT_DATE, REGION_NAME, AVG_UPTIME_PCT, MIN_UPTIME_PCT, TOWER_COUNT, POOR_TOWERS, EXCELLENT_TOWERS |
| LATENCY_TABLE | SNAPSHOT_DATE, TOWER_NAME, TOWER_TYPE, REGION_NAME, AVG_LATENCY_MS, P95_LATENCY_MS, P99_LATENCY_MS, AVG_JITTER_MS |
| PACKET_LOSS_TABLE | SNAPSHOT_DATE, TOWER_NAME, TOWER_TYPE, REGION_NAME, AVG_PACKET_LOSS_PCT, MAX_PACKET_LOSS_PCT, HIGH_LOSS_EVENTS, PACKET_LOSS_GRADE |
| MTTR_TABLE | SNAPSHOT_DATE, TOWER_NAME, TOWER_TYPE, REGION_NAME, AVG_MTTR_MINUTES, AVG_ACKNOWLEDGE_MIN, AVG_DISPATCH_MIN, REPAIR_TYPE, REPAIR_COUNT |
| OUTAGE_ANALYSIS_TABLE | SNAPSHOT_DATE, TOWER_NAME, TOWER_TYPE, REGION_NAME, ROOT_CAUSE, SEVERITY, OUTAGE_COUNT, UNPLANNED_COUNT, TOTAL_AFFECTED_USERS, AVG_DURATION_MIN |
| CAPACITY_PLANNING_TABLE | SNAPSHOT_DATE, TOWER_NAME, TOWER_TYPE, REGION_NAME, AVG_UTILIZATION_PCT, PEAK_UTILIZATION_PCT, TOWER_CAPACITY_MBPS, AVG_PEAK_THROUGHPUT, AVG_FORECAST_DEMAND, CAPACITY_STATUS, FORECAST_STATUS |
| VENDOR_PERFORMANCE_TABLE | SNAPSHOT_DATE, VENDOR, EQUIPMENT_TYPE, EQUIPMENT_COUNT, AVG_LATENCY_MS, AVG_PACKET_LOSS_PCT, AVG_THROUGHPUT_MBPS, OUTAGE_COUNT |
| SLA_COMPLIANCE_TABLE | SNAPSHOT_DATE, TOWER_NAME, TOWER_TYPE, REGION_NAME, TIER_NAME, OVERALL_SLA_STATUS, UPTIME_SLA_STATUS, LATENCY_SLA_STATUS, PACKET_LOSS_SLA_STATUS |
| EXECUTIVE_SUMMARY_TABLE | SNAPSHOT_DATE, OVERALL_UPTIME_PCT, AVG_LATENCY_MS_30D, AVG_PACKET_LOSS_PCT_30D, AVG_MTTR_MINUTES, UNPLANNED_OUTAGES_30D, ACTIVE_TOWERS, TOTAL_AFFECTED_USERS_30D, AVG_THROUGHPUT_MBPS_30D, UPTIME_DELTA_MOM, LATENCY_DELTA_MOM, PACKET_LOSS_DELTA_MOM, MTTR_DELTA_MOM, OUTAGES_DELTA_MOM, THROUGHPUT_DELTA_MOM |
| LATENCY_TREND_TABLE | METRIC_DATE, REGION_NAME, AVG_LATENCY_MS, P95_LATENCY_MS, MEASUREMENT_COUNT |
| MTTR_TREND_TABLE | WEEK_START, REGION_NAME, AVG_MTTR_MINUTES, REPAIRS |
| THROUGHPUT_TABLE | SNAPSHOT_DATE, TOWER_NAME, TOWER_TYPE, REGION_NAME, AVG_THROUGHPUT_MBPS, PEAK_THROUGHPUT_MBPS, AVG_BANDWIDTH_UTIL_PCT, THROUGHPUT_GRADE |
| MTTD_TABLE | SNAPSHOT_DATE, TOWER_NAME, TOWER_TYPE, REGION_NAME, ROOT_CAUSE, AVG_MTTD_MINUTES, P90_MTTD_MINUTES, DETECTION_PCT_OF_TOTAL_DURATION, INCIDENT_COUNT |

## Configuration Steps

1. **Install the application** from the Snowflake Marketplace.
2. **Bind table references** — On first launch, the app will prompt you to bind each of the 14 required table references via the Snowsight permissions dialog. You must grant SELECT access to tables (or views) matching the schemas described below.
3. **Verify data** — After binding, the sidebar "Data Load Status" section shows row counts per table. Confirm all tables have data loaded.
4. **Explore the dashboard** — Use the filter bar (Region, Tower Type, Year/Quarter/Month, Date Range) to slice the analytics.

## Stored Procedures & UDFs

| Object | Type | Description |
|--------|------|-------------|
| `config.register_single_reference` | Procedure | Callback invoked by Snowsight when a consumer binds/unbinds a table reference |
| `config.setup_views` | Procedure | Creates internal views pointing to bound reference tables |

## Required Privileges

The app requests **SELECT** on each bound table. No other privileges are required. All analytics run in-place on your Snowflake warehouse — no data leaves your account.

## Example SQL Commands

After installing the app, you can manually bind references via SQL if preferred:

```sql
-- Grant access to your data
GRANT USAGE ON DATABASE <YOUR_DB> TO APPLICATION NETWORK_PERF_ANALYTICS_APP;
GRANT USAGE ON SCHEMA <YOUR_DB>.<YOUR_SCHEMA> TO APPLICATION NETWORK_PERF_ANALYTICS_APP;
GRANT SELECT ON TABLE <YOUR_DB>.<YOUR_SCHEMA>.<YOUR_TABLE> TO APPLICATION NETWORK_PERF_ANALYTICS_APP;

-- Bind a reference
CALL NETWORK_PERF_ANALYTICS_APP.config.register_single_reference(
  'NETWORK_UPTIME_TABLE', 'ADD',
  SYSTEM$REFERENCE('TABLE', '<YOUR_DB>.<YOUR_SCHEMA>.<YOUR_TABLE>', 'PERSISTENT', 'SELECT')
);
```

Repeat the `register_single_reference` call for each of the 14 references listed below.

## Expected Categorical Column Values

The dashboard renders charts that group, color, or filter by categorical fields. For charts to display meaningful results, these columns **must** contain values from the sets listed below. Rows with values outside these sets will still be stored but may not appear in visual summaries.

### Grade Fields

| Column | Table(s) | Expected Values |
|--------|----------|-----------------|
| `UPTIME_GRADE` | NETWORK_UPTIME_TABLE | `Excellent`, `Good`, `Fair`, `Poor` |
| `PACKET_LOSS_GRADE` | PACKET_LOSS_TABLE | `Excellent`, `Good`, `Fair`, `Poor` |
| `THROUGHPUT_GRADE` | THROUGHPUT_TABLE | `Excellent`, `Good`, `Fair`, `Poor` |

### Status Fields

| Column | Table(s) | Expected Values |
|--------|----------|-----------------|
| `CAPACITY_STATUS` | CAPACITY_PLANNING_TABLE | `Healthy`, `Monitor`, `Warning - Plan Upgrade`, `Critical - At Capacity` |
| `FORECAST_STATUS` | CAPACITY_PLANNING_TABLE | `WITHIN CAPACITY`, `OVER CAPACITY` |
| `OVERALL_SLA_STATUS` | SLA_COMPLIANCE_TABLE | `COMPLIANT`, `NON-COMPLIANT` |
| `UPTIME_SLA_STATUS` | SLA_COMPLIANCE_TABLE | `PASS`, `FAIL` |
| `LATENCY_SLA_STATUS` | SLA_COMPLIANCE_TABLE | `PASS`, `FAIL` |
| `PACKET_LOSS_SLA_STATUS` | SLA_COMPLIANCE_TABLE | `PASS`, `FAIL` |

### Outage & Repair Fields

| Column | Table(s) | Expected Values |
|--------|----------|-----------------|
| `SEVERITY` | OUTAGE_ANALYSIS_TABLE | `Low`, `Medium`, `High`, `Critical` |
| `ROOT_CAUSE` | OUTAGE_ANALYSIS_TABLE, MTTD_TABLE | `Power Failure`, `Hardware Fault`, `Software Bug`, `Weather Damage`, `Configuration Error`, `Network Congestion` |
| `REPAIR_TYPE` | MTTR_TABLE | `Software Reset`, `Hardware Replace`, `Configuration Fix`, `Power Restore` |

### Dimension Fields

| Column | Table(s) | Expected Values |
|--------|----------|-----------------|
| `TOWER_TYPE` | Most tables | `5G`, `4G`, `3G` |
| `REGION_NAME` | Most tables | Any string (e.g., `North`, `South`, `East`, `West`, `Central`) |
| `TIER_NAME` | SLA_COMPLIANCE_TABLE | `Enterprise`, `Business`, `Consumer` |
| `VENDOR` | VENDOR_PERFORMANCE_TABLE | Any string (e.g., `Ericsson`, `Nokia`, `Huawei`, `Samsung`) |
| `EQUIPMENT_TYPE` | VENDOR_PERFORMANCE_TABLE | `Radio Unit`, `Baseband`, `Antenna`, `Small Cell` |

## Permissions

The app requests SELECT access on bound tables. All analytics run in-place on your Snowflake warehouse. No data leaves your account.

## Version History

| Version | Date | Notes |
|---------|------|-------|
| 1.0.0 | 2026-05-20 | Initial marketplace release |
