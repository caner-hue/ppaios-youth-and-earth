---
name: draft-reply
description: Compose a customer-service reply using brand voice. Draft only by default.
---

## What this skill does
Takes a message + classification + customer context, produces a reply that sounds like the brand. Always saves as Gmail draft via Composio. Never sends in Phase 1.

## Context
- Read `references/tone-of-voice.md`, `references/brand-guidelines.md`.
- Read brand brain for KIND2-specific phrasing rules.
- Read `references/policies.md` CS allow-list.

## Inputs
- Original message id.
- Classification + suggested action (from `triage-incoming`).
- Customer order history if available (Shopify Composio: `shopify_get_customer_orders`).

## Workflow
1. Pull customer order history (if customer id resolvable).
2. Draft a reply that:
   - Opens warm but direct.
   - Addresses the actual question (no fluff).
   - Cites order number, tracking link, refund timeline as appropriate.
   - Closes with a clear next step.
   - UK English. No em dashes. No therapy speak.
3. Run `brand-voice-check` skill on the draft.
4. Create Gmail draft via `gmail_create_draft` Composio tool.
5. Append `outputs.json` row: `{ts, message_id, draft_id, category, ready_to_send: false, needs_human_review: true}`.

## Rules
- Never invent product claims, dates, refund amounts, or tracking numbers.
- Never apologise gratuitously. Acknowledge briefly, fix.
- For complex/angry messages, keep it short and route to Caner.
- Never put a discount code in a draft without Director approval.
