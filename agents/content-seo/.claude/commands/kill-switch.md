---
name: kill-switch
description: Check, set, or release the global PPAIOS kill switch.
---

## What this skill does
Manages `~/.dev-houston/KILL_SWITCH`. Every spend/publish/send skill must call `kill-switch check` before acting.

## Modes

### check
Returns `{active: true}` if the file exists, else `{active: false}`. Read-only.

### set
Creates the file with timestamp + reason inside. Logs to `shared-ledger.jsonl`. iMessages Caner confirming.

### release
Deletes the file. Logs to `shared-ledger.jsonl`. iMessages Caner confirming.

## Rules
- Only the Director, Caner via iMessage (`KILL` / `RESUME`), or an Analytics P1 alert can `set`.
- Only Caner via iMessage can `release`.
- Audit log is append-only. Every state change has a row.
