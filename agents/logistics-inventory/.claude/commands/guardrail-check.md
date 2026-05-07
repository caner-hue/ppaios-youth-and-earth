---
name: guardrail-check
description: Wrap any spend, publish, or send action. Returns allow/block + reason.
---

## What this skill does
Single chokepoint every irreversible action passes through. Reads `policies.json` + `budgets.json` for the workspace, applies the rule, writes a decision to `shared-ledger.jsonl`.

## Context
- Workspace `policies.json` and `budgets.json`.
- Workspace `shared-ledger.jsonl` (append-only audit log).
- `~/.dev-houston/KILL_SWITCH` (kill toggle).

## Inputs
```json
{
  "agent": "paid-ads",
  "action": "increase_budget",
  "channel": "meta",
  "params": { "campaign_id": "...", "delta_pct": 25, "delta_gbp": 5 }
}
```

## Workflow
1. If `KILL_SWITCH` exists, return `{allow: false, reason: "kill switch active"}` and append to ledger.
2. Look up the rule for `{agent, action}` in `policies.json`.
3. Compare params against thresholds.
4. If under threshold: `{allow: true}` and append to ledger.
5. If over threshold: `{allow: false, reason: "...", needs_approval: true}` and queue an iMessage approval request.
6. Always append the full decision (timestamp, agent, action, params, decision, reason) to `shared-ledger.jsonl`.

## Rules
- Default-deny on any unknown action. Add the rule explicitly.
- Never silently allow. Every call writes a ledger entry.
- Approval requests time out after 4 hours; on timeout, default to deny.
