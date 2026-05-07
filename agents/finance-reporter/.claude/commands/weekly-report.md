---
name: weekly-report
description: Director's Monday rollup across all agents and brands.
---

## What this skill does
Pulls the past 7 days from every agent's `outputs.json` per workspace, joins with KPI snapshots, and produces a single Monday review.

## Context
- All workspaces under `~/.dev-houston/workspaces/`.
- For each: `shared-ledger.jsonl`, `outputs.json`, `alerts.jsonl`, `kpi-snapshots/`, `reviews/`.

## Workflow
1. For each brand:
   a. Group last 7 days of `outputs.json` by agent.
   b. Pull KPI deltas from `kpi-snapshots/`: ROAS, CAC, CVR, AOV, email rev share, BSR, CS SLA, social engagement, creator deliveries, reseller pipeline movement, Amazon spend, contribution margin.
   c. List Guardrail blocks not unblocked.
   d. List P1 alerts.
2. Per brand, write the section: what shipped, what's stuck, three suggested moves, KPI table.
3. Combine into `reviews/{YYYY-MM-DD}-rollup.md`.
4. Email Caner via Gmail MCP.
5. iMessage one-liner: brand-by-brand traffic light + top action.
6. Append to `shared-ledger.jsonl`.

## Rules
- Always include "what worked".
- Three moves max per brand. Discipline beats noise.
- If a brand is silent (no outputs in 7 days), say so explicitly. Silence is news.
- Caner's reply tokens (`OK`, `STOP {channel}`, `KILL`, `RESUME`, free text) drive the next-week setup.
