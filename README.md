# SPK DBA Automation

A collection of DBA automation tools built for a 4-node Oracle 19c RAC / Data Guard
banking production environment (RHEL 9.x). Each subfolder is a standalone project
with its own README covering requirements, design, and usage.

## Projects

| Folder | Description | Status |
|---|---|---|
| [`oracle_db_self_monitoring_alerting`](./oracle_db_self_monitoring_alerting) | Self-monitoring RAC/DG health-check & email alerting system (16-point check, 30-min cron, twice-daily digest) | Pending file upload |
| [`sftp_self_service_tool`](./sftp_self_service_tool) | Interactive Python/paramiko self-service SFTP tool for developers | Pending file upload |
| [`rac_diagnostic_awr_collection`](./rac_diagnostic_awr_collection) | Consolidated diagnostic/AWR collection pipeline for the RAC cluster | Pending file upload |
| [`security_audit_evidence_reports`](./security_audit_evidence_reports) | SQL*Plus → Word pipeline generating security audit evidence reports for bank IT auditors | Pending file upload |

## Environment context

- Oracle 19c RAC, 4 nodes, Grid Infrastructure, Data Guard DR
- RHEL 9.x
- Production banking environment — scripts favor OS authentication, audit-ready
  output, and config-driven behavior over hardcoded values
