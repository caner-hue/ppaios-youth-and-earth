---
name: budget-pacing
description: Daily check that paid spend is on pace and within £20/day cap. Pauses runaway campaigns. Approves underspend top-ups inside Guardrail limits.
---

## What this skill does
Daily 9am + 3pm cron. Reads spend across Meta/TikTok/Google/Amazon. Compares to per-channel and total caps. Acts on overspend (pause), flags underspend (recommend reallocate).

## Context
- `../budgets.json` — `phase_4_entry.daily_total_paid_cap = 20`, per-channel caps.
- `../policies.json` — overrides.
- `../shared-ledger.jsonl` — past 24h decisions.
- Per-channel insights via Composio.

## Inputs
None. Cron-driven. Optional `{ as_of: "ISO date" }` for testing.

## Workflow
1. **Kill switch check.** If active, log no-op and exit.
2. **Pull today's spend** per channel via Composio:
   - Meta: `composio execute META_GET_AD_ACCOUNT_INSIGHTS` with `time_range: today, breakdowns: [campaign_name]`.
   - TikTok: `composio execute TIKTOK_GET_REPORT` with `data_level: AUCTION_AD, dimensions: [campaign_id], date_range: today`.
   - Google: `composio execute GOOGLE_ADS_GET_CUSTOMER_METRICS` with `today`.
   - Amazon (Phase 5): skip until wired.
3. **Compute totals:**
   - `total_today_gbp = sum(channel_spend)`
   - `cap_today = budgets.phase_4_entry.daily_total_paid_cap` (£20)
   - `pct_used = total_today_gbp / cap_today`
   - `hours_remaining = 24 - hour_of_day(UK)`
   - `pace = pct_used / (hour_of_day / 24)` (1.0 = on pace)
4. **Decision tree:**
   - `pct_used >= 1.0` → **breach.** Pause every active campaign across all channels. Append P1 to alerts.jsonl. iMessage Caner. Set Guardrail flag `daily_paid_breach: true` to prevent un-pause until tomorrow.
   - `pace > 1.5 AND pct_used >= 0.8` → high pace, late breach risk. Pause 1 highest-spending campaign per channel as a brake. Log to ledger.
   - `pace < 0.5 AND hour_of_day >= 18` → big underspend. Generate a "top up tomorrow" recommendation in `outputs/pacing-recs.jsonl` for Director's morning review. Do NOT auto-increase budgets.
   - `0.5 <= pace <= 1.5` → on pace. No action. Log a heartbeat.
5. **Write spend snapshot** to `outputs/spend-snapshots/<YYYY-MM-DD>-<HH>.json`:
   - Per channel: spend_gbp, impressions, clicks, purchases, roas.
   - Totals.
   - Pace metric.
   - Decision taken (none / pause / recommend / breach).
6. **Ledger append:** `{ts, agent: "paid-ads", action: "pacing_check", total_gbp, pace, decision}`.

## Rules
- NEVER raise a budget unless explicitly invoked by Caner. Pacing only pauses or recommends.
- Per-campaign pause uses `composio execute META_UPDATE_CAMPAIGN` with `status: PAUSED`. Never delete.
- All pause actions go through `guardrail-check` first (action: `pause_campaign`). Pause is technically "safer than launch" but still passes through audit.
- After a breach, no campaign may be unpaused for 24h without Caner saying `RESUME` via iMessage.
- Phase 4 first 14 days: any pause OR top-up recommendation iMessages Caner immediately. After 14 days, only breaches escalate live.

## Failure modes
- One channel API is down → still process the other channels. Log the missing channel as P2.
- Multiple channels down (>1) → log P1, do not pause anything (we're flying blind), iMessage Caner.
- Spend = 0 across all channels at hour 18+ → either kill switch is active (verify) or all campaigns are paused (acceptable). Log informational.

## Output
```json
{
  "as_of": "ISO ts",
  "channels": {
    "meta":   { "spend_gbp": 0, "pct_of_channel_cap": 0 },
    "tiktok": { "spend_gbp": 0, "pct_of_channel_cap": 0 },
    "google": { "spend_gbp": 0, "pct_of_channel_cap": 0 }
  },
  "totals": { "spend_gbp": 0, "cap_gbp": 20, "pct_used": 0, "pace": 1.0 },
  "decision": "none" | "pause_high_spender" | "pause_all_breach" | "recommend_topup",
  "campaigns_paused": [],
  "next_check": "ISO ts"
}
```
