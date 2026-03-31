---
description: "Phase 3 of Learn Everything: Multi-expert adversarial learning to expose blind spots. Dynamically generates expert personas and launches parallel agents to critically question the knowledge base. Use when user says /learn-challenge or wants adversarial review."
---

# Learn Everything — Phase 3: Adversarial Learning

**Domain**: $ARGUMENTS

## Goal

Use multi-perspective critical questioning to identify and fill knowledge gaps. Different expert viewpoints expose different blind spots.

## Step 1: Verify & Load

1. Read `domains/{slug}/meta.md` — Phase 2 must be complete
2. 读取 `domains/{slug}/curriculum.md` — 获取模块清单
3. 读取 `domains/{slug}/modules/` 下所有模块文件，编译各模块的核心内容摘要

**ISOLATION RULE**: Only access `domains/{slug}/` and `templates/`.

## Step 2: Generate Expert Personas

Based on the specific domain, dynamically generate **3-4 expert personas** that would surface different kinds of blind spots. Choose perspectives that challenge the KB from different angles:

- Technical implementation vs. theoretical foundations
- Practitioner vs. academic
- Ethics/society vs. business value
- Adjacent field vs. core field

Present the personas to the user and ask if they'd like to adjust.

## Step 3: Parallel Adversarial Agents

**Launch all persona agents simultaneously.** Each agent receives:
- Summary of skeleton + key findings from paper notes
- Their persona identity and perspective
- Instructions to produce:
  1. **3-5 Critical Questions** exposing KB gaps
  2. **Blind Spots** — topics completely absent that matter
  3. **Challenged Assumptions** — claims that are oversimplified or misleading
  4. **2-3 Missing Resources** — papers/books that should be added
  - 输出盲点时必须标明「属于哪个模块」（参考 curriculum.md 中的模块编号）

## Step 4: Synthesize & Research

After all agents return:
1. Compile a consolidated blind spot map with severity ratings (Critical/High/Medium)
2. For each critical/high blind spot:
   - Use WebSearch to research the gap
   - 将新发现的内容**直接补充到 `domains/{slug}/modules/` 中对应模块文件的相关小节**，并标注来源
3. Update `skeleton.md` — fix concept dependency map, add new open problems

## Step 5: Save & Present

1. Save full Q&A session to `domains/{slug}/qa/{date}.md`
2. Update `meta.md`: mark Phase 3 complete
3. Present: blind spot count by severity, new concepts added, structural corrections made
4. Suggest: "Ready to generate your learning path (`/learn-path {topic}`)?"
