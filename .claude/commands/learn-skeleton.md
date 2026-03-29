---
description: "Phase 1 of Learn Everything: Find top 3-5 researchers and 10 most-cited papers to map a field's knowledge skeleton. Use when user says /learn-skeleton or needs to build the foundational structure of a domain."
---

# Learn Everything — Phase 1: Knowledge Skeleton

**Domain**: $ARGUMENTS

## Goal

Map the field by identifying the top 3-5 researchers and 10 most-cited/influential works. This is the foundation everything else builds on.

## Step 1: Verify Domain

1. Normalize topic to slug
2. Read `domains/{slug}/meta.md` — if Phase 1 already complete, ask user if they want to redo
3. If domain doesn't exist, tell user to run `/learn {topic}` first

**ISOLATION RULE**: Only access `domains/{slug}/` and `templates/`.

## Step 2: Parallel Research

Launch **all searches simultaneously** — do not wait between them:

- `"{topic} most influential researchers"`
- `"{topic} most cited papers survey"`
- `"{topic} seminal foundational papers"`
- `"{topic} recent survey review paper 2024 2025"`
- Semantic Scholar API: `https://api.semanticscholar.org/graph/v1/paper/search?query={topic}&limit=20&fields=title,authors,year,citationCount,venue,abstract`

Then do a second round of targeted searches for specific researchers/papers discovered in round 1. Again, run these in parallel.

## Step 3: Synthesize

Cross-reference all results to identify:
- **Top 3-5 researchers**: Those appearing repeatedly as authors of high-impact work
- **Top 10 papers/books**: Ranked by citation count, covering foundational + survey + recent breakthrough

## Step 4: Write Output

Write two files in parallel using templates from `templates/`:
- `domains/{slug}/skeleton.md` — knowledge skeleton with concept map
- `domains/{slug}/researchers.md` — researcher profiles

## Step 5: Update & Present

1. Update `meta.md`: mark Phase 1 complete
2. Update `domains/index.md`
3. Present skeleton to user in a clean table
4. Ask: "Scope correct? Want to adjust? Ready for Phase 2 (`/learn-kb {topic}`)?"
