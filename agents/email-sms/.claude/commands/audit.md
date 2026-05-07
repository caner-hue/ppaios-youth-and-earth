---
name: audit
description: Scan PPAIOS state, surface gaps, rank by leverage. Run per-agent or workspace-wide.
---

## What this skill does
Surveys the current state of PPAIOS (or a single agent) and produces a ranked gap report. Used Friday evenings by the Director and ad-hoc by any agent that wants to assess its own readiness.

## Context
- Read `references/company-context.md`, `references/policies.md`.
- Read the workspace `connections.json`, `kpi-targets.json`, `budgets.json`, `policies.json`.
- Read every agent's `outputs.json` for the past 7 days.

## Workflow
1. Build a checklist of expected capabilities per agent (from `agents/{id}/houston.json` and `agents/{id}/CLAUDE.md`).
2. For each capability, mark: connected (yes/no), last successful run, error rate, gap severity.
3. Run the AIOS Five Questions:
   - What was done manually more than twice?
   - What tool/data source is being checked that PPAIOS can't see?
   - Which skill ran daily? (mark for routine promotion)
   - What broke? (add a rule or reference doc)
   - What new capability would save the most time?
4. Rank gaps by leverage = (frequency × time saved per run) / build cost.
5. Write `outputs/audit-{YYYY-MM-DD}.md`.
6. Append summary to `shared-ledger.jsonl`.
7. iMessage Caner the top 3 gaps if mode == workspace-wide.

## Rules
- Never propose more than 5 next actions in one report. Discipline beats noise.
- Always include a "what worked this week" section. Bias toward truth, not flattery.
- If KILL_SWITCH exists, audit still runs (read-only).
