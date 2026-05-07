---
name: social-post
description: Draft platform-native posts for IG/TikTok/X/LinkedIn. Schedule via Composio. Live publish gated.
---

## What this skill does
Takes a hook or campaign theme, produces platform-specific posts with the right voice/length/format. Schedules into the queue (when available via Composio) or saves to Notion Content Calendar as drafts.

## Context
- Tone-of-voice + brand brain.
- Recent post performance (when available).
- Content calendar (Notion DB).

## Inputs
- Channel (IG / TikTok / X / LinkedIn).
- Theme or hook.
- Asset (image/video reference, if any).
- Target post date.

## Workflow
1. Pick the format that fits the channel:
   - IG: 1 image or carousel + 80–150 word caption + 3–5 niche hashtags.
   - TikTok: 7-second hook + 30–60s narrative arc + caption + 1–2 trending sounds (note only).
   - X: 220–270 chars, lead with the proof, no hashtags unless brand-relevant.
   - LinkedIn: 800–1200 chars, P1 first sentence, story or framework, no fake "thoughts?" CTA.
2. Run `brand-voice-check`.
3. Save to Notion Content Calendar with status=Draft.
4. Append `outputs.json`.

## Rules
- Never publish live without approval (Phase 1–3).
- Never auto-DM or auto-comment.
- IG/TikTok: respect platform safe-zones for any embedded text.
- LinkedIn: no engagement bait. No "double tap if". No "agree?".
