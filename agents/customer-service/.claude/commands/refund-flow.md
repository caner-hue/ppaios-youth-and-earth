---
name: refund-flow
description: Process a refund request. Drafts only first 30 days; allow-list automation after.
---

## What this skill does
Handles full refund/return flow: verify order, check policy, draft reply with refund details, optionally issue Shopify refund (gated).

## Context
- Read `policies.json` (KIND2 has a 60-day refund window, ≤£50 auto for existing customers post-30-day).
- Read brand brain for KIND2 returns process.
- Always run `guardrail-check` before any irreversible action.

## Inputs
- Original message id.
- Customer email + order number (extracted by `triage-incoming`).
- Reason given.

## Workflow
1. Look up order via Shopify Composio: `shopify_get_order` + `shopify_get_customer`.
2. Verify:
   - Order exists.
   - Within 60-day window.
   - Item not flagged as final-sale.
   - Customer has prior orders (counts as "existing" for policy).
3. Decide:
   - In-policy + ≤£50 + existing customer + post-30-day: prepare refund.
   - Out-of-policy or >£50 or new customer or first 30 days: draft only, route to Caner.
4. If preparing refund:
   - Call `guardrail-check` with `{action: "issue_refund", params: {amount_gbp, customer_status}}`.
   - On allow: create Shopify refund via Composio `shopify_create_refund`.
   - On deny: queue approval iMessage to Caner.
5. Draft reply via `draft-reply` skill, embedding refund confirmation if applicable.
6. Log to `outputs.json` + `shared-ledger.jsonl`.

## Rules
- Never refund without an order match.
- Never issue partial refund > requested.
- If customer demands refund but item isn't returnable, draft a polite alternative (store credit, replacement) rather than escalating immediately.
