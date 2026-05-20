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

## Permissions

The app requests SELECT access on bound tables. All analytics run in-place on your Snowflake warehouse. No data leaves your account.

## Version History

| Version | Date | Notes |
|---------|------|-------|
| 1.0.0 | 2026-05-20 | Initial marketplace release |
