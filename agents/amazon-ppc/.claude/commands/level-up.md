---
name: level-up
description: Suggest the next connection or skill to build, ranked by leverage.
---

## What this skill does
Recommends the single highest-leverage next move for PPAIOS or a specific agent.

## Context
- Read `outputs/audit-*.md` (latest).
- Read `references/company-context.md`.
- Read `agents/*/CLAUDE.md` for current capabilities.

## Workflow
1. Pull the latest audit gap list.
2. For each gap, score: time saved per week × frequency / build effort (hours).
3. Pick the top 1 (workspace-wide) or top 3 (per agent).
4. Draft the build spec: what to add, where it lives, which APIs, expected time to ship.
5. Write `outputs/level-up-{YYYY-MM-DD}.md`.
6. iMessage Caner with a one-line summary.

## Rules
- Never recommend building something for a problem that hasn't happened twice.
- Prefer "boring cron + API call" over "agentic skill" when judgement isn't required.
- Be honest if the answer is "nothing material to add this week".
