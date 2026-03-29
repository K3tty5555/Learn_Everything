---
description: "Phase 4 of Learn Everything: Generate a personalized learning path based on the knowledge base. Asks about user's background to customize depth and focus. Use when user says /learn-path or wants a study roadmap."
---

# Learn Everything — Phase 4: Learning Path Generation

**Domain**: $ARGUMENTS

## Goal

Produce a layered, actionable learning roadmap customized to the user's existing knowledge and goals.

## Step 1: Verify & Load

1. Read `domains/{slug}/meta.md` — at least Phase 1 must be complete (Phase 2+3 recommended)
2. Read ALL domain files: skeleton, researchers, paper notes, concepts, QA logs

**ISOLATION RULE**: Only access `domains/{slug}/` and `templates/`.

## Step 2: Understand the Learner

Before generating the path, ask the user:
1. **Your background**: What's your current role? What do you already know about this field?
2. **Your goal**: Why are you learning this? (career switch, deepen expertise, academic research, curiosity?)
3. **Time commitment**: How much time per week can you dedicate?

Use their answers to customize depth, starting point, and emphasis.

## Step 3: Generate Customized Path

Based on the full KB + user context, create a multi-level learning path.

**LANGUAGE RULE**: All content MUST be in Chinese (see CLAUDE.md "Content Language" section). Section headings, descriptions, milestones — all Chinese. Only source citations and universally-known acronyms (AI, ML, PM) stay in English.

**FORMATTING RULE**: Use **Markdown tables** for any structured/comparison data. NEVER use code blocks (```) to fake visual layouts like side-by-side columns or ASCII art comparisons — these render poorly in DOCX. If you need to show "what you have vs. what you need", use a 2-column table.

Structure:

- **你的起点**: 学习者已有能力 vs 需要补强的方向（用表格，不用代码块）
- **Level 1（基础）**: 前置知识 + 核心概念 + 基础阅读
- **Level 2（进阶）**: 关键论文/技术 + 实践练习
- **Level 3（高级）**: 前沿研究 + 开放问题
- **Level 4+（专项）**: 基于学习者目标的专项路线

Each level includes:
- 具体阅读材料及推荐顺序，附**为什么现在读它**
- 需要掌握的关键概念
- 与学习者实际工作关联的实践练习
- 里程碑（checkbox）用于追踪进度

Include a **recommended starting point** — don't always start at Level 1 if the user has prior knowledge.

## Step 4: Generate Support Materials

If the domain has enough content, also create:
- `cheatsheet-week1.md` — first week action plan with concrete daily tasks
- Concept quick-reference cards if there's a glossary

## Step 5: Save & Present

1. Save to `domains/{slug}/learning-path.md` (and cheatsheet if created)
2. Update `meta.md`: mark Phase 4 complete
3. Present the path with visual structure
4. Suggest: "Ready to publish as DOCX for offline reading (`/learn-publish {topic}`)?"
