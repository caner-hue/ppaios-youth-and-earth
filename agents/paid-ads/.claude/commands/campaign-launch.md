---
name: campaign-launch
description: Launch a new paid ads campaign on Meta/TikTok/Google. Drafted by default, gated >£10/day, blocked by kill switch.
---

## What this skill does
Creates a new ad campaign as paused/draft. Never goes live without explicit approval. Wraps every spend-related call in `guardrail-check` and `kill-switch`.

## Context (read these before acting)
- `../brand-brain.md` — KIND2 voice, ICP, hero offer.
- `../policies.json` — KIND2 publish/spend overrides.
- `../budgets.json` — `phase_4_entry.daily_total_paid_cap = £20`, per-channel caps.
- `../references/meta-marketing-api.md` (or tiktok / google equivalents).
- `../references/tone-of-voice.md` — banned phrases, no em dashes.
- `../shared-ledger.jsonl` — append decision log.

## Inputs
```json
{
  "channel": "meta" | "tiktok" | "google",
  "objective": "OUTCOME_SALES" | "OUTCOME_LEADS" | "OUTCOME_TRAFFIC",
  "name": "<descriptive>",
  "daily_budget_gbp": <number>,
  "audience": "<description or saved audience id>",
  "creative_id": "<from creative producer's library>",
  "landing_page_url": "https://kind2.com/<slug>",
  "start_paused": true
}
```

## Workflow
1. **Kill switch check.** Run `kill-switch check`. If active, abort with `{ status: "halted", reason: "kill_switch_active" }`.
2. **Guardrail check.** Call with `{action: "launch_campaign", channel, params: {proposed_daily_gbp: daily_budget_gbp}}`.
   - On `allow: false` → write the campaign spec to `outputs/drafts/campaign-<slug>-<date>.md` (NOT to the platform), iMessage Caner with the spec + reason, return `{status: "draft_pending_approval"}`.
   - On `allow: true` → proceed.
3. **Brand voice check.** Pass campaign name + ad copy variants through `brand-voice-check`. If violations, rewrite before submitting.
4. **Composio call** to create the campaign:
   - Meta: `composio execute META_CREATE_CAMPAIGN` with `status: PAUSED`, daily_budget in lowest denomination (pence).
   - TikTok: `composio execute TIKTOK_CREATE_CAMPAIGN` with `operation_status: DISABLE`.
   - Google: `composio execute GOOGLE_ADS_CREATE_CAMPAIGN` with `status: PAUSED`.
5. **Verify creation.** Read the campaign back. Confirm status is paused.
6. **Append to ledger:** `{ts, agent: "paid-ads", action: "launch_campaign", channel, campaign_id, daily_budget_gbp, status: "paused", landing_page_url}`.
7. **Notify Caner via iMessage:** "Meta campaign '<name>' created paused at £<x>/day. Reply UNPAUSE <id> to launch or KILL to abort."
8. **Output:** structured row to `outputs/campaigns.jsonl` with all fields above + provenance.

## Rules
- ALWAYS create paused; never auto-unpause.
- Hard cap respected: if `daily_budget_gbp > 10` Guardrail rejects automatically; iMessage approval required.
- Never duplicate KIND2's existing live campaign names. Read existing campaigns first via `composio execute META_GET_CAMPAIGNS` and check.
- One primary CTA per ad.
- UK English, no em dashes, no hype, voice from `../references/tone-of-voice.md`.
- Phase 4 first 14 days = ALL launches require Caner approval, regardless of budget.
- Refuse to set objective `OUTCOME_AWARENESS` on KIND2 (the brand has documented CAC targets; awareness wastes the cap).

## Failure modes
- Composio `META_CREATE_CAMPAIGN` returns 4xx → write to `../alerts.jsonl` severity=P2, abort.
- Brand voice fails → log violations, rewrite, re-check; if still failing after 2 attempts, escalate to Director.
- KIND2 ad account suspended → Composio returns auth error; iMessage Caner P1 immediately.
