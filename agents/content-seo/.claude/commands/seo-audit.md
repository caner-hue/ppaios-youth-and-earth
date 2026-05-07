---
name: seo-audit
description: Audit a Shopify site for technical SEO + content gaps. Output ranked gap list.
---

## What this skill does
Pulls site structure via Shopify Composio + Google Analytics traffic data + (optionally) Search Console. Produces a ranked gap list: technical issues, content gaps, internal-link opportunities.

## Context
- Brand brain for ICP and product map.
- KPI targets for organic traffic and conversion.
- Existing blog posts via Shopify.

## Workflow
1. Inventory:
   - All published products + collections + pages + blog posts via Shopify.
   - Top 50 landing pages by sessions via Google Analytics (Composio).
2. Technical checks (per page):
   - Title tag present, ≤60 chars.
   - Meta description present, ≤160 chars.
   - H1 present and unique.
   - Schema markup present (Article / Product / FAQPage).
   - Image alt text on hero image.
3. Content gaps:
   - Compare top 10 keywords driving traffic against competitor sites (web search).
   - Find content gaps where KIND2/PP doesn't have a ranking page.
4. Internal linking:
   - Pages with high rank but low internal links pointing to them.
5. Rank gaps by leverage = (estimated monthly search volume × CTR uplift) / build effort.
6. Write to `outputs/seo-audit-{YYYY-MM-DD}.md`. Top 5 picks at the top.

## Rules
- Don't propose more than 5 next moves (discipline beats noise).
- Never recommend keyword stuffing or thin AI content.
- Always check whether a recommendation cannibalises an existing ranking page.
