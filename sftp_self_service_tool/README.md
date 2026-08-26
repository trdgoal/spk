# SFTP Self-Service Tool

Interactive Python/paramiko tool that lets developers browse, upload, download,
and search files on SFTP servers themselves — removing the daily manual
requests to the DBA team to check `sftp_details.xml` and perform transfers.

> **Status:** Structure created, script not yet uploaded to this repo.

## Background

Config layout: a master directory `/u01/IAP/Config_Gdlp/` contains one folder
per application (`Cas_app`, `Rfia_app`, `rfca_app`, etc.), each with
`configfiles/sftp_details.xml` (filename is sometimes misspelled
`sftp_detials.xml`). Each XML holds one or more `<sftp>` entries with
`id`/`name`/`remoteIp`/`port`/`username`/`password` and
`connectionTimeOut`/`channelTimeOut` (minutes).

App servers have internet/repo access, so `pip install` works — unlike the
air-gapped audit-report servers, which is why this tool uses Python + paramiko
rather than bash/expect/lftp.

## Flow

1. List available app folders → user picks an app.
2. Display the full raw `sftp_details.xml` content (no password masking —
   credentials stay in the file as-is).
3. User enters the `sftp` entry id to connect.
4. Show remote folder tree/navigation (`cd`/`ls`/`tree`).
5. User picks an operation: **upload**, **download**, or **search**.
   - Upload: local path + filename(s) (single or multiple) + destination remote path.
   - Download: remote path/pattern + local destination.
   - Search: path + filename search pattern.
6. Explicit **Y/N confirmation** required before any upload or download proceeds.
7. Conflict handling: if a file already exists locally or remotely, prompt
   per file (no auto-skip/auto-overwrite).

## Logging

Single shared log file that all script executions append to; entries older
than 7 days are purged automatically on each run.

## Notes

- Interactive only — no non-interactive CLI mode.
- Unit-tested: XML parsing against real sample data, app discovery, log purge,
  and mock-SFTP navigation flow all passing.

## Folder layout

```
sftp_self_service_tool/
├── README.md
└── scripts/
    └── sftp_tool.py
```
