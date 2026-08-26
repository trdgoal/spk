# Oracle DB Self-Monitoring & Alerting

Self-monitoring script for a 4-node Oracle 19c RAC cluster with Data Guard DR
(nodes: `iap-db-dr`, `iap-db2-dr`, `iap-db3-dr`, `iap-db4-dr`). Runs 16 health
checks every 30 minutes and sends HTML, color-coded email alerts. Email-only
alerting — no auto-remediation.

> **Status:** Structure created, scripts not yet uploaded to this repo.

## Architecture

- Shell script(s) collect checks per node (OS-level) and once for the shared
  RAC database (DB-level).
- Python (`smtplib`) evaluator/mailer applies thresholds and sends
  HTML-formatted, color-coded mail.
- Config-driven via `thresholds.yaml`, `nodes.yaml`, `exclusions.yaml` — checks
  can be added/removed/modified without touching core logic.
- SMTP credentials (user, password, host, port, mail_to, mail_cc) read from
  root's `.oraenv` file — nothing hardcoded in the scripts.
- Auth is OS-level only (`/ as sysdba`); no stored DB passwords.
- Passwordless SSH (oracle/grid users) used for OS-level checks across nodes.

## The 16 checks

1. DB/listener down (standalone or RAC)
2. Filesystem usage (`df -hT`) on all mountpoints > 90%
3. ASM diskgroup usage > 90%
4. Data Guard archive log sync gap (primary vs DR)
5. Long-running queries > 10 min (excludes RMAN/batch jobs via config-driven exclusion list)
6. Blocking sessions / deadlocks / locks (full session, query, blocker-blockee detail)
7. Row-lock contention
8. Tablespace usage > 90%
9. ORA- error codes in the alert log (last 30 minutes only, per run — **no 6-hour suppression**, alerts every run for errors in that window even if repeated)
10. RMAN backup status — checks only yesterday's run, alerts only on failure
11. Cluster status (any CRS issue)
12. CPU > 65%
13. Antivirus agent process (`ds_agent`, shows as `ds_asm` in `top`) — alerts when the process itself hits 100% CPU **and** overall server CPU > 60% (known to hang the server)
14. Sessions/processes > 60% of limit
15. Locked accounts where `account_status = LOCKED` and `oracle_maintained = 'N'` (username + status)
16. OS user password expiry for `oracle`, `root`, `grid`, `admin`, `SOCVA`, `scguard` — alerts if expiring within 7 days or already expired and not reset

## Alerting policy

- Default: alert once per breach, re-notify after 6 hours if the breach persists.
- Exception: check #9 (ORA errors) alerts every 30-minute run for errors found in that window, with no suppression.
- Twice-daily consolidated status digest (09:30 and 17:30) — separate cron job,
  reuses the same collection engine (`lib_collect.sh`). Shows all 16 checks:
  green "GOOD" when healthy, full color-coded detail otherwise. Always sends
  (no suppression).

## Deployment

See [`docs/README_DEPLOYMENT.md`](./docs/README_DEPLOYMENT.md) once uploaded —
covers cron setup (30-min alert job + twice-daily digest job) and `.oraenv`
sourcing.

## Folder layout

```
oracle_db_self_monitoring_alerting/
├── README.md
├── config/
│   ├── thresholds.yaml
│   ├── nodes.yaml
│   └── exclusions.yaml
├── scripts/
│   ├── lib_collect.sh          # shared collection engine (alert + digest jobs)
│   ├── (per-node OS check script)
│   ├── (SQL check scripts)
│   ├── (Python evaluator/mailer)
│   └── (cron wrapper sourcing .oraenv)
└── docs/
    └── README_DEPLOYMENT.md
```
