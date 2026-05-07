---
name: brand-voice-check
description: Run drafted text against tone-of-voice.md. Flag violations and rewrite.
---

## What this skill does
QA gate before any drafted copy goes out. Checks for banned constructions, AI tells, em dashes, therapy speak. Returns violations + a rewritten draft.

## Context
- Read `references/tone-of-voice.md`.
- Read brand-specific overrides if `brand-brain.md` declares them.

## Inputs
- The draft text.
- Channel (email / blog / social / CS / outreach).

## Workflow
1. Scan draft for:
   - Em dashes (—).
   - Contrast framing ("this isn't X, it's Y").
   - Engagement bait ("here's the thing", "what most people get wrong", "the real question is").
   - AI tells (delve, navigate the complexities, in today's fast-paced, in the realm of, leverage as a verb).
   - Hype words (game-changing, revolutionary, unlock, 10x, crush it).
   - Excessive ellipses, exclamation marks, gendered patronising terms.
2. Per violation, log: `{rule, snippet, suggestion}`.
3. If any violation, rewrite the offending passages and return a clean draft.
4. Confirm: UK English, sentences not bullets (unless natively a list), short paragraphs.

## Output
```json
{
  "passed": true|false,
  "violations": [{"rule":"em_dash","snippet":"...","suggestion":"..."}],
  "rewritten_draft": "..."
}
```

## Rules
- Always run before save/send for any human-facing copy.
- If ambiguous, preserve original but flag for review.
- Never weaken specificity (numbers, brand names, dates) when rewriting.
