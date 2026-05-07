---
name: email-broadcast
description: Plan and prepare a Klaviyo campaign. Drafts auto, send always gated.
---

## What this skill does
Plans cadence, segments, and copy for a Klaviyo broadcast. Creates the campaign as a draft via Klaviyo Composio. Sending is always Caner-approved in Phase 1–2.

## Context
- Read brand brain, tone-of-voice.
- Read recent campaign performance (last 5 sends) via Klaviyo `metric_aggregates`.
- Read content calendar from Notion DB (when wired).

## Inputs
- Theme/offer (from Director or content calendar).
- Target segment (default: "Engaged" + "VIP").
- Send window (UK time).

## Workflow
1. Pull last 5 broadcast results: open rate, CTR, revenue/recipient.
2. Identify what worked, draft 3 subject options (A/B/C).
3. Draft body in brand voice. UK English. Clear hierarchy: hook → proof → offer → CTA.
4. Run `brand-voice-check`.
5. Composio Klaviyo: `klaviyo_create_campaign` with template + segment.
6. Predicted size: pull segment count.
7. Run `guardrail-check` for `{action: "send_broadcast", channel: "email", params: {recipients}}`.
   - ≤5k auto: skill returns "draft ready, send approved".
   - >5k: queue Caner approval iMessage.
8. Append `outputs.json` row: `{ts, campaign_id, subject, segment, recipients, status}`.

## Rules
- Never send without explicit Caner approval in Phase 1–2.
- Never use a discount > 15% without Director sign-off.
- Subject line max 50 chars (most clients truncate after).
- Always include a one-click unsubscribe footer.
