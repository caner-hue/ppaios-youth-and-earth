---
name: wiki-build
description: Turn raw inputs into an LLM-queryable knowledge wiki with backlinks and an index.
---

## What this skill does
Reads everything in `knowledge/raw/`, produces structured pages in `knowledge/wiki/` with backlinks, an `index.md`, and a `log.md`. Drops query-time tokens vs reading raw transcripts.

## Context
- Source: `knowledge/raw/` (transcripts, decks, meeting notes, framework docs).
- Output: `knowledge/wiki/` (one page per topic), `knowledge/index.md`, `knowledge/log.md`.

## Workflow
1. Inventory `raw/`: list files, sizes, modified dates.
2. For each file:
   a. Identify topics, people, tools, techniques, decisions.
   b. Create or update `wiki/{topic}.md` with sections: summary, key points, references back to raw, backlinks to related wiki pages.
3. Build `index.md` grouped by domain (Growth, Ops, Knowledge, Tools).
4. Append a row to `log.md`: timestamp, files processed, pages created/updated.
5. If Obsidian is installed, the resulting folder opens directly as a vault.

## Rules
- Never paraphrase raw content into wiki pages over 30-word chunks. Summarise, then link.
- Backlinks are mandatory. Every wiki page references the source file in raw.
- Wiki pages stay under 500 lines. Split if longer.
- Run on demand or weekly via routine.
