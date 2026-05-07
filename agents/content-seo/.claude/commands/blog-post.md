---
name: blog-post
description: Draft a blog post, publish to /blog/draft/ via Shopify. Live publish always gated.
---

## What this skill does
Takes a topic + target keyword, produces a full blog draft with schema markup, internal links, and a publish-ready frontmatter. Lands at `/blog/draft/<slug>` for review. Caner promotes to live.

## Context
- Read brand brain, tone-of-voice.
- Read `references/seo-audit.md` patterns if available.
- Pull existing PP/KIND2 blog posts (Shopify Composio: `shopify_list_articles`) for internal linking + cannibalisation check.

## Inputs
- Topic + primary keyword.
- Target word count (default 1200–1800).
- Audience (DTC operators / KIND2 customers / press).

## Workflow
1. Brief: outline H2s based on what's already ranking and where the gap is.
2. Research: pull last 30 days of related news/data via web search if needed.
3. Draft:
   - Title (H1) ≤60 chars.
   - First paragraph proves the operator's POV in <80 words.
   - 4–6 H2 sections.
   - Specific numbers, real brand names, documented outcomes.
   - 2–4 internal links to existing posts.
   - JSON-LD `Article` schema in frontmatter.
4. Run `brand-voice-check`.
5. Shopify Composio: `shopify_create_article` with `published: false`, handle prefixed `draft-`.
6. Append `outputs.json` row + Notion Content Calendar entry.

## Rules
- Never publish live (always gated).
- Never invent statistics. If no source, omit the stat.
- Never use AI tells (see `tone-of-voice.md`).
- Internal-link to at least 2 PP/KIND2 posts where genuinely relevant.
