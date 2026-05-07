---
name: triage-incoming
description: Read Gmail inbox, score by business priority, draft replies for the easy ones, queue the rest for human review.
---

## What this skill does
Daily 7am routine for Customer Service. Pulls last 24h of inbox via Gmail (Composio), scores each message, drafts replies for the obvious ones, files the rest into Notion Tasks DB (or a markdown queue if Notion not yet wired).

## Context
- Read `references/company-context.md`, `references/tone-of-voice.md`, `references/policies.md`.
- Read `policies.json` overrides for CS allow-list.
- First 30 days = drafts only. After 30 days = allow-list autosend.

## Inputs
- Gmail unread, last 24h.
- Brand inbox: `caner@kind.community` (KIND2) and any forwarded support inboxes.

## Workflow
1. Use Gmail Composio tool: `gmail_list_messages` with query `is:unread newer_than:1d`.
2. For each message:
   a. Extract sender, subject, body, in-reply-to.
   b. Classify: Order issue | WISMO | Refund/return | Product question | PR/biz dev | Spam | Personal.
   c. Score priority P1–P3 using Brand Brain + recent CS dossier.
3. Auto-actions per category:
   - Spam → Gmail label "Spam_review", archive.
   - WISMO → call `wismo-reply` skill.
   - Refund/return → call `refund-flow` skill (drafts only first 30 days).
   - Product question → drafted reply via `draft-reply` skill.
   - PR/biz dev → escalate to Director, queue in Notion Tasks DB.
4. Append a row to `outputs.json`: `{ts, message_id, category, priority, action, draft_id?}`.
5. End report:
   ```
   {
     "scanned": N,
     "auto_replied": N,
     "drafted": N,
     "escalated": N,
     "spam": N,
     "P1_alerts": [...]
   }
   ```

## Rules
- Never send an email unless `policies.json` allow-list is satisfied AND `guardrail-check` passes AND kill switch is off.
- Never quote >15 words from inbound message in any output to the user.
- If inbox volume >30 in 24h, flag to Director (unusual spike).
