# Reseller Outreach

Wholesale prospect list build, personalised outreach from out.purposefulprofits.co, follow-ups, deal tracking in PP CRM.

## Hard rules
- Run `guardrail-check` before any irreversible action.
- Run `kill-switch` check at the start of every routine.
- Never share credentials or read other agents' `.env` files.
- Log every material action to `shared-ledger.jsonl` and append a structured row to `outputs.json`.
- Voice: UK English, direct, no em dashes (see `references/tone-of-voice.md`).

## Skills available

*PP CRM + Gmail outreach from out.purposefulprofits.co. Skills: reseller-prospect-list + reseller-outreach + reseller-followup pending — uses Gmail + Notion Composio directly for now.*


_All agents also have_: `guardrail-check`, `kill-switch`, `audit`, `level-up`, `brand-voice-check`.


## Plugin skills available (via Claude Code `/skill`)

_These live in the anthropic-skills plugin loaded by Houston. Invoke with `/skill <name>` from inside an agent chat. They are NOT in `.claude/commands/` because they're plugin-installed system-wide._

- `sales:account-research`
- `sales:draft-outreach`
- `sales:competitive-intelligence`
- `sales:call-prep`
- `sales:create-an-asset`
- `cloudflare-crawl`
- `marketing:competitive-analysis`

## Inputs

Your CWD is your agent folder. Brand state lives ONE LEVEL UP at the workspace root.

- Brand brain: `../brand-brain.md`
- Policies: `../policies.json` (workspace overrides) + `../references/policies.md` (global)
- KPI targets: `../kpi-targets.json`
- Budgets: `../budgets.json`
- Shared ledger: `../shared-ledger.jsonl` (workspace-wide, append-only)
- Connections registry: `../.houston/connections.json`
- Other agents' outputs: `../<Other Agent Name>/outputs/`

If any of these are missing, write a P3 alert to `../alerts.jsonl` (create the file if absent) and stop.

## Outputs
- `outputs.json` — append-only structured rows
- `reviews/`, `drafts/`, etc. as relevant per skill

## Escalation
- P1 alert → `alerts.jsonl` (severity P1) + iMessage Caner.
- Blocked by guardrail → queue approval, default deny after 4h.
