---
description: "Systematically learn any domain: find top researchers, build sourced knowledge base, adversarial Q&A, generate learning path, publish to DOCX. Use when user wants to learn about a topic, study a field, or says /learn. This is the entry point — it routes to phase-specific sub-commands."
---

# Learn Everything: $ARGUMENTS

You are helping the user systematically learn about **$ARGUMENTS**.

## Step 1: Initialize

1. Normalize topic to a slug (e.g., "quantum computing" → "quantum-computing")
2. Check if `domains/{slug}/` exists:
   - **Exists**: Read `domains/{slug}/meta.md`, summarize progress, show the menu below
   - **New**: Create `domains/{slug}/` with subdirectories `papers/`, `concepts/`, `qa/`, `published/`. Create `meta.md` from `templates/meta.md`. Add one-line entry to `domains/index.md`. Then auto-start Phase 1.

**ISOLATION RULE**: Only access files in `domains/{slug}/` and `templates/`. Never touch other domain directories.

## Step 2: Show Available Actions

Based on current progress, present what the user can do:

```
📍 Domain: {topic} | Current: {phase status}

Available commands:
  /learn-skeleton {topic}   — Phase 1: Find top researchers & papers
  /learn-kb {topic}         — Phase 2: Build sourced knowledge base
  /learn-challenge {topic}  — Phase 3: Multi-expert adversarial Q&A
  /learn-path {topic}       — Phase 4: Generate learning path
  /learn-publish {topic}    — Phase 5: Publish to DOCX with images

  Or just ask a question about {topic} — I'll answer from the KB.
```

## Step 3: Route or Answer

- If user specifies a phase → tell them to run the corresponding command
- If user says "continue" → auto-run the next incomplete phase
- If user asks a question → enter Q&A mode:
  1. Read relevant KB files (skeleton, paper notes, concepts)
  2. Answer with source attribution
  3. If answer requires info not in KB → WebSearch, then add to KB
  4. Append Q&A to `domains/{slug}/qa/{date}.md`
