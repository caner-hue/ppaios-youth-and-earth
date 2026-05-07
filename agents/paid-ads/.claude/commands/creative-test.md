---
name: creative-test
description: Generate creative test plan for an existing campaign. Picks variants, sets duration, writes to draft. Live deployment via campaign-launch only.
---

## What this skill does
Takes a campaign id + a creative library, builds a structured A/B/C test plan. Doesn't push creatives live. Outputs a plan for `campaign-launch` to consume.

## Context
- `../brand-brain.md`, `../references/tone-of-voice.md`.
- Recent campaign performance via `composio execute META_GET_CAMPAIGN_INSIGHTS`.
- Creative library: `../../Creative Producer/outputs/library.jsonl` (when wired).
- VOC sources: customer reviews mined by Creative Producer.

## Inputs
```json
{
  "campaign_id": "<from campaign-launch>",
  "test_type": "creative" | "headline" | "audience" | "landing_page",
  "variant_count": 3,
  "test_duration_days": 7,
  "primary_metric": "purchase" | "add_to_cart" | "ctr" | "cvr"
}
```

## Workflow
1. **Read latest 14 days** of campaign insights for the parent campaign.
2. **Identify the strongest base** — the ad set already performing best.
3. **Generate variants:**
   - For `creative` test: pull 3 creative ids from Creative Producer library matching the campaign's audience and objective.
   - For `headline` test: write 3 variants. Each ≤40 chars. UK English. Lead with proof.
   - For `audience` test: 3 audience definitions (lookalike pct vs interest stack vs broad).
   - For `landing_page` test: 3 URL variants (different hero offer / proof points).
4. **Run `brand-voice-check`** on every text variant.
5. **Compute test sizing:** what daily budget is needed per variant to hit statistical significance in `test_duration_days`?
   - Rule of thumb: 50 conversions per variant minimum at current campaign CVR.
   - If projected budget × duration > daily cap allocation, flag as P3 and propose longer duration.
6. **Write plan** to `outputs/test-plans/<campaign_id>-<YYYY-MM-DD>.md`:
   - Hypothesis (1 sentence).
   - Variants (table).
   - Daily budget per variant.
   - Success criteria.
   - Decision rule on day 7 (winner promoted, losers paused).
7. **Append ledger:** `{ts, agent: "paid-ads", action: "create_test_plan", campaign_id, variant_count}`.
8. **Notify Director** via shared ledger; do NOT iMessage Caner unless the plan exceeds budget allocation.

## Rules
- Never deploy. This skill only plans.
- 3-7 variants max. More than 7 is unscientific at our budget level.
- Test duration min 5 days, max 14. Below 5 = noise, above 14 = creative fatigue muddies signal.
- Always include 1 control variant matching current best performer.
- VOC-mined headlines win 70% of the time vs generic — prefer them when available.
- If the campaign is currently outperforming targets by >2x, flag "do not change a winning campaign" and abort the test plan.

## Failure modes
- Creative library empty for the campaign's audience → log P3, escalate to Creative Producer for asset production.
- Insights API rate-limited → backoff per Composio retry; if 3 fails, log P2 and resume on next routine cycle.
