# RAC Diagnostic & AWR Collection Pipeline

Consolidated diagnostic/AWR collection pipeline for a 4-node Oracle 19c RAC
environment, designed for single-command execution from Node 1.

> **Status:** Structure created, scripts not yet uploaded to this repo.

## Design goals

- Production readiness — safe to run against a live banking RAC cluster.
- Audit integrity — SHA256 manifests generated for collected output.
- Correct trust model — collection/execution boundaries designed deliberately
  rather than assumed.

## Folder layout

```
rac_diagnostic_awr_collection/
├── README.md
└── scripts/
```

*(Details will be filled in as the original scripts and any further
requirements are uploaded.)*
