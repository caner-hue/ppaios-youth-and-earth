---
name: wismo-reply
description: "Where is my order?" — pull tracking via Shopify, draft response with link.
---

## What this skill does
Boring 80% of CS volume. Customer asks about delivery. Pulls tracking, drafts response with the link. Auto-send allowed after 30 days for existing customers.

## Context
- Shopify Composio for order + fulfillment.
- KIND2 ships via Sendcloud; tracking links land in Shopify fulfillment data.

## Workflow
1. Extract order id or customer email from message.
2. Shopify Composio: `shopify_get_order` → fulfillments + tracking number/url.
3. If shipped:
   - Draft: order, ship date, carrier, tracking link, expected delivery.
4. If pending fulfillment >2 business days:
   - Draft: acknowledge delay, give realistic ship-date, escalate to Logistics agent.
5. If lost (tracking shows no movement >5 days post-dispatch):
   - Draft acknowledgement + "we're chasing the carrier" line.
   - Open a Logistics agent task in Notion Tasks DB.
6. Always run `brand-voice-check` before saving draft.
7. Auto-send (after 30 days, existing customer): `gmail_send` via Composio + `guardrail-check`.

## Rules
- Always include the tracking URL, never just the number.
- Never promise a delivery date the carrier hasn't confirmed.
- If tracking is private (e.g. Royal Mail 24/48), use the receipt/reference only.
