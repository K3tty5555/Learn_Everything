---
description: "Learning verification for Learn Everything: Generate quiz questions from the knowledge base to test understanding. Supports multiple question types, tracks mastery, and links back to source materials. Use when user says /learn-quiz, '考考我', '测验', 'quiz me', or wants to verify their learning."
---

# Learn Everything — Quiz: Test Your Understanding

**Domain**: $ARGUMENTS

## Goal

Generate targeted quiz questions from the knowledge base to verify real understanding — not just recognition, but the ability to apply, analyze, and explain.

## Step 1: Load Knowledge Base

1. Read `domains/{slug}/meta.md` — at least Phase 2 must be complete
2. Read all concept files in `concepts/`
3. Read key findings from papers in `papers/`
4. Read `learning-path.md` if it exists (to know which level the user is at)

**ISOLATION RULE**: Only access `domains/{slug}/` and `templates/`.

## Step 2: Ask Quiz Preferences

Ask the user:
- **Scope**: "Test me on everything, or a specific level/topic?" (e.g., "just Level 1 concepts" or "just the HCAI framework")
- **Quantity**: "How many questions? (recommend 5-10)"
- **Difficulty**: "Foundation / Intermediate / Advanced / Mixed?"

## Step 3: Generate Questions

Create a mix of question types based on the KB content:

### Question Types (aim for variety)

**Type 1: Concept Check (知识点确认)**
> "用你自己的话解释什么是[概念]。"
- Tests: Can they define it without copying the KB?
- Source: concept files

**Type 2: Scenario Application (场景应用)**
> "你在设计[具体产品功能]时，Amershi的18条准则中哪3条最相关？为什么？"
- Tests: Can they apply frameworks to real situations?
- Source: paper notes + user's work context

**Type 3: Compare & Contrast (对比分析)**
> "[概念A]和[概念B]的核心区别是什么？在什么场景下选A而非B？"
- Tests: Deeper understanding of nuances
- Source: multiple concept files

**Type 4: Critical Thinking (批判性思考)**
> "[某个KB中的观点]——你认为这个观点有什么局限性？"
- Tests: Can they think beyond the KB?
- Source: adversarial learning Q&A

**Type 5: Situational Judgment (情境判断)**
> "你的AI模型在城市学校准确率92%，农村学校78%。你会怎么做？"
- Tests: Decision-making ability
- Source: concepts + user's domain context

## Step 4: Interactive Q&A

Present questions **one at a time**. After user answers each question:

1. **Evaluate the answer** against KB content
2. **Score**: ✅ Solid / ⚠️ Partial / ❌ Needs Review
3. **Feedback**: What was good, what was missing, with specific KB source references
4. **If wrong**: Link back to the exact concept file or paper note to review
5. Move to next question

## Step 5: Summary Report

After all questions are answered, generate a mastery report:

```
📊 Quiz Results: {domain}
━━━━━━━━━━━━━━━━━━━━
✅ Solid:    {n}/{total} — {concepts list}
⚠️ Partial:  {n}/{total} — {concepts list}
❌ Review:   {n}/{total} — {concepts list}

📖 Recommended Review:
- Re-read: concepts/{concept}.md — {reason}
- Re-read: papers/{paper}.md — {reason}
```

Append results to `domains/{slug}/qa/quiz-{date}.md` for tracking progress over time.
