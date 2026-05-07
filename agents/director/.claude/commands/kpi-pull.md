---
name: kpi-pull
description: Snapshot KPIs from GA4, Shopify, Klaviyo, Stripe, Meta into kpi-snapshots/.
---

## What this skill does
Boring cron job. No judgement. Pulls fresh metrics every day at 8am to a single JSON snapshot per brand.

## Context
- API references: `references/ga4-api.md`, `references/shopify-api.md`, `references/klaviyo-api.md`, `references/stripe-api.md`, `references/meta-marketing-api.md`.
- Output: `~/.dev-houston/workspaces/{brand}/.houston/kpi-snapshots/{YYYY-MM-DD}.json`.

## Schema
```json
{
  "date": "YYYY-MM-DD",
  "brand": "kind2",
  "shopify": { "sessions": 0, "orders": 0, "revenue_gbp": 0, "cvr": 0, "aov_gbp": 0 },
  "ga4": { "users": 0, "sessions": 0, "engaged_sessions": 0 },
  "klaviyo": { "campaign_revenue_gbp": 0, "flow_revenue_gbp": 0, "list_size": 0 },
  "meta": { "spend_gbp": 0, "impressions": 0, "clicks": 0, "purchases": 0, "roas": 0 },
  "tiktok": { "spend_gbp": 0, "roas": 0 },
  "google_ads": { "spend_gbp": 0, "roas": 0 },
  "amazon": { "spend_gbp": 0, "acos": 0, "tacos": 0, "bsr_top": null },
  "stripe": { "consultancy_revenue_gbp": 0 }
}
```

## Workflow
1. Pull data via Composio tools (no raw API keys):
   - `shopify_*` for sessions, orders, revenue, AOV.
   - `googleanalytics_runReport` for users, sessions, engaged.
   - `klaviyo_metric_aggregates` for campaign + flow revenue, list size.
   - `googleads_*` for spend + ROAS (when Phase 4 wired).
   - Stripe via API-direct (`STRIPE_API_KEY_RO`) for consultancy revenue.
2. Write the snapshot file.
3. Append to `kpi-snapshots/index.jsonl` (one line per day, for trend queries).
4. If any metric is missing or stale, write to `alerts.jsonl` with severity=P3.

## Rules
- No agentic interpretation. Pure data fetch.
- Idempotent. Re-running produces the same file.
- Failures bubble to the Analytics agent for diagnosis.
