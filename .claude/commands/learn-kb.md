---
description: "Phase 2 of Learn Everything: Build a sourced knowledge base by analyzing papers in parallel. Zero hallucination tolerance — every claim has a source. Use when user says /learn-kb or wants to build the knowledge base for a domain."
---

# Learn Everything — Phase 2: Knowledge Base Construction

**Domain**: $ARGUMENTS

## Goal

Create structured, sourced notes for each core paper/book. Every factual claim must have a source annotation. Zero hallucination tolerance.

## Step 1: Verify Domain

1. Read `domains/{slug}/meta.md` — Phase 1 must be complete
2. Read `domains/{slug}/skeleton.md` to get the paper list

**ISOLATION RULE**: Only access `domains/{slug}/` and `templates/`.

## Step 2: Classify Papers

Split the paper list into two groups:
- **Open access** (academic papers with public preprints/PDFs) → can analyze immediately
- **Books/paywalled** → ask user if they have PDFs, otherwise work from public info

Present the classification and ask: "Which books do you have PDFs for? The rest I'll work from public information (abstracts, author talks, reviews) and mark as `[Based on public information only]`."

## Step 3: Parallel Paper Analysis — THE KEY OPTIMIZATION

**Launch multiple Agent subprocesses to analyze papers in parallel**, not one-by-one.

Split papers into batches of 2-3 and launch one Agent per batch. Each agent receives:
- The paper-note template from `templates/paper-note.md`
- Instructions to:
  - For each assigned paper, use WebSearch/WebFetch to find abstracts, summaries, key content
  - Create a structured note following the template
  - **Source every claim**: `[Source: Author, Year, Section X]`
  - Mark unverified info as `[Unverified]`
  - Mark public-info-only papers as `[Based on public information only — full paper review recommended]`
  - Write results to `domains/{slug}/papers/{author-year}.md`

Example: 10 papers → 4 parallel agents (3+3+2+2 papers each)

**NEVER fabricate paper content.** If an agent can't find information about a paper, it must say so explicitly rather than guess.

## Step 4: Concept Extraction

**LANGUAGE RULE**: All concept file content MUST be in Chinese (see CLAUDE.md "Content Language" section). Headings, definitions, explanations — all Chinese. Only source citations stay in English. Technical terms without standard Chinese translations use bilingual format: `中文（English）`.

**FORMATTING RULE**: Use Markdown tables for structured/comparison data. NEVER use code blocks to fake visual layouts.

After all paper agents complete:
1. Read all paper notes
2. Identify recurring key concepts across papers
3. Create concept files in `domains/{slug}/concepts/` — each with:
   - 定义
   - 来源（哪篇论文提出）
   - 相关概念
   - 对实践者的意义
   - 来源标注

## Step 5: Update & Present

1. Update skeleton's Concept Map section with discovered relationships
2. Update `meta.md`: mark Phase 2 complete
3. Update `domains/index.md`
4. Summarize what was analyzed: papers covered, concepts extracted, source quality
5. Suggest: "Ready for Phase 3 adversarial review (`/learn-challenge {topic}`)?"
