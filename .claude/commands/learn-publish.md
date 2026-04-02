---
description: "Phase 5 of Learn Everything: Convert knowledge base to DOCX (for Feishu) or slides (for team sharing), with optional embedded images. Requires explicit user confirmation before any image generation due to cost. Use when user says /learn-publish, 'export', 'publish', '导出', '发布', '做PPT', or wants DOCX/slides."
---

# Learn Everything — Phase 5: Publish

**Domain**: $ARGUMENTS

## Goal

Convert MD knowledge base files into publishable formats:
- **DOCX** → import into Feishu for multi-device reading
- **Slides** → for team knowledge sharing

## Pre-check: Illustration Coverage

Before publishing, check if images are in place:

```
💡 配图检查：domains/{slug}/assets/manifest.md 当前覆盖了多少模块？
   - 若有模块缺少配图，建议先运行 `/learn-illustrate {topic}`
   - 图片通过 manifest.md 自动嵌入 DOCX，不需要手动插入
   - 可以跳过：若本次只需要文字版 DOCX
```

Ask the user: "是否先补充配图？（Y = 先运行 /learn-illustrate / N = 直接发布）"

---

## Step 1: Verify & Inventory

1. Read `domains/{slug}/meta.md`
2. List all MD files in the domain directory
3. Create `domains/{slug}/published/` if needed
4. Create `domains/{slug}/assets/` if needed (for images — **outside** `published/`)

**ISOLATION RULE**: Only access `domains/{slug}/` and `templates/`.

**FOLDER PURITY RULE**: `published/` must contain **only DOCX files** — no scripts, no images, no subdirectories. The user imports this entire folder into Feishu. Any non-DOCX artifacts (scripts, images, intermediate files) go into `domains/{slug}/assets/` or `domains/{slug}/` root.

## Step 2: Ask User What to Publish

```
📦 Publishing Options:

Output format:
  A) DOCX files → Feishu import (all MD → individual DOCX)
  B) Slide deck → Team sharing (key frameworks as presentation)
  C) Both

Which would you like?
```

## Step 3: Image Cost Gate — MANDATORY

**⚠️ IMAGE GENERATION IS EXPENSIVE. NEVER generate images without explicit user confirmation.**

Before ANY image generation, you MUST:

1. **Scan all files** and identify which ones would benefit from visuals
2. **Present a numbered list** with exactly what each image would contain:

```
🎨 Image Generation Plan:

These files would benefit from embedded visuals:
  1. 知识骨架 → 概念依赖流程图 (1 image)
  2. HCAI框架 → 四象限模型图 (1 image)
  3. AI产品评估 → 三层金字塔图 (1 image)
  4. AI PM vs 传统PM → 对比信息图 (1 image)
  5. 学习路径 → 学习路线图 (1 image)

Total: 5 images
Cost note: Each image costs API credits.

These files will be text-only DOCX (no images needed):
  - 论文笔记 × 10 files
  - QA报告 × 1 file
  - 其他概念文件 × 4 files

Options:
  Y) Generate all 5 images → then build DOCX/slides
  N) Skip all images → text-only DOCX/slides
  Pick specific numbers (e.g., "1,3,5") → only generate those
```

3. **WAIT for user's explicit response** before proceeding
4. If user says N → skip to Step 5 (reading order) then Step 6 (content quality) then Step 7 (text-only DOCX)
5. If user picks specific numbers → only generate those

**This gate is non-negotiable. Never auto-generate images.**

## Step 4: Generate Images (only after confirmation)

For confirmed images only, launch parallel agents:

- Use `baoyu-infographic` (user-level skill) for structured data (frameworks, matrices, hierarchies)
- Use `baoyu-image-gen` (user-level skill) for conceptual illustrations
- Each agent saves to `domains/{slug}/assets/` (**not** inside `published/`)

All image agents can run in parallel since they're independent.

## Step 5: Determine Reading Order

DOCX files are for self-study — the reader opens a folder and needs to know **what to read first** without a separate guide. File names must encode reading order via prefixes.

### Naming Rules

| Prefix | Meaning | Examples |
|--------|---------|---------|
| `00-` | Entry point or reference (read first / look up anytime) | `00-学习路径（先读这个）.docx`, `00-术语表（随查随用）.docx` |
| `01-` ~ `09-` | Sequential reading in recommended order | `01-欧盟AI法案（EU AI Act）要点.docx` |
| `附-` | Background material, not required reading | `附-知识骨架.docx`, `附-核心研究者.docx` |

### Chinese-First Naming Rule

File names MUST use Chinese as the primary language. For terms without widely-accepted Chinese translations, use **bilingual format**: `中文名（English）`.

| Scenario | Format | Example |
|----------|--------|---------|
| Has standard Chinese name | Pure Chinese | `用户心理学.docx`, `AI成本经济学.docx` |
| Has Chinese name but English acronym is well-known | Chinese + parenthetical English | `以人为本AI框架（HCAI）.docx`, `AI工厂（AI Factory）.docx` |
| English term is the standard (no Chinese equivalent) | Chinese explanation + parenthetical English | `上市策略（GTM）与竞争壁垒.docx` |
| Universal acronym (AI, ML, PM) | Use directly, no translation needed | `AI产品评估.docx` |

### How to determine order

1. Read `learning-path.md` to find the **recommended Level sequence** (the "学习顺序建议" section)
2. Map each concept/knowledge-base DOCX to the Level it belongs to
3. Assign numbers following the Level order (lower Level number = lower file number)
4. Within the same Level, order by the learning path's sub-section sequence
5. Files that are process artifacts (skeleton, researchers, QA logs) get `附-` prefix
6. The learning path itself and glossary/cheatsheet get `00-` prefix

### Classification guide

| Category | Prefix | Rationale |
|----------|--------|-----------|
| learning-path.md | `00-…（先读这个）` | Master roadmap, must be first |
| cheatsheet / glossary | `00-…（随查随用）` | Reference, no fixed position |
| Concept files in learning path order | `01-` ~ `09-` | Core learning content |
| skeleton.md, researchers.md | `附-` | Research process records |
| QA / challenge reports | `附-` | Supplementary |
| Paper notes | Usually skip export; if exported, `附-` | Too academic for casual reading |

## Step 6: Content Quality Pass — Before DOCX Generation

Before converting to DOCX, scan each MD file and fix content issues:

### 6.1 Chinese-First Content Rule

All content inside the DOCX must follow CLAUDE.md's "Content Language" rule:
- **Headings**: Chinese (e.g., `## 你的起点` not `## Your Starting Position`)
- **Body text**: Chinese
- **Technical terms**: bilingual `中文（English）` for terms without standard Chinese translations
- **Source citations**: keep original language `[Source: Author, Year]`

If the source MD has English headings (from older generation), **translate them during conversion**.

### 6.2 Formatting Quality Rules

| MD source pattern | Correct DOCX rendering | Wrong rendering |
|-------------------|----------------------|-----------------|
| Markdown table (`\| ... \|`) | DOCX table with borders and header row | Plain text |
| Code block with comparison/layout data | Convert to DOCX table | Monospace text block |
| Code block with actual code | Monospace formatted paragraph | Table |
| Bullet list | DOCX bullet list | Plain text with `-` chars |
| `> blockquote` | Indented italic paragraph | Plain text |

**Key anti-pattern**: NEVER render a code block that contains structured comparison data (like "已有 vs 需要补强") as monospace text. Detect these and convert to proper 2-column tables with header row.

### 6.3 De-AI Humanization (with professional constraint)

Before DOCX generation, run content through `Humanizer-zh` to remove AI-generated writing patterns, BUT with a critical constraint:

**Preserve professional precision.** This is a knowledge-transfer project — overly colloquial language can distort technical accuracy.

| Remove | Keep |
|--------|------|
| AI套话（"值得注意的是"、"总而言之"） | 专业术语原样保留 |
| 夸大的修饰（"极其关键"、"至关重要"） | 精确的技术描述 |
| 三段式排比、否定式排比 | 因果推理链 |
| 模糊归因（"研究表明"没有出处） | 带出处的论断 `[Source: ...]` |
| 过度连接词（"此外"、"与此同时"开头） | 简洁直接的陈述 |

**不要做的事**：不要把"过拟合指模型在训练集上表现优异但在新数据上泛化能力下降"改写成"就是说模型只会死记硬背"。保持原有的技术精度。

### 6.4 DOCX Generation Tool

Use `minimax-docx` skill for DOCX creation. It provides:
- OpenXML-based generation with proper table/heading/list support
- CJK typography (East Asian font configuration, justified alignment)
- Design principles compliance (white space, hierarchy, font scale)
- Validation pipeline (XSD + business rules)

Reference: `.claude/skills/minimax-docx/SKILL.md` (bundled in this project)

## Step 7: Generate DOCX (parallel agents)

Launch parallel agents to convert MD → DOCX. Each agent handles a batch:

| Agent | Files | Notes |
|-------|-------|-------|
| A | skeleton.md + researchers.md | `附-` prefix, embed concept map if generated |
| B | learning-path.md + cheatsheet.md | `00-` prefix, embed roadmap if generated |
| C | All papers/*.md | Usually skip; if requested, `附-` prefix |
| D | All concepts/*.md | `01-09` prefix per reading order, embed framework images if generated |
| E | All qa/*.md | `附-` prefix |

Each agent uses `minimax-docx` to create DOCX with:
- **Ordered filename prefixes** (see Step 5)
- **Chinese-first filenames** with bilingual parentheticals for English-origin terms (see Step 5)
- **Chinese-first content** with bilingual terms where needed (see Step 6.1)
- **Proper table formatting** for all structured/comparison data (see Step 6.2)
- Embedded images from `domains/{slug}/assets/` where applicable (images stay **outside** `published/`)
- CJK typography: East Asian fonts (微软雅黑 or SimSun), justified alignment, auto-spacing
- Feishu-compatible: Table Grid style, standard headings, no advanced OpenXML features
- All DOCX saved to `published/` — **no other file types allowed in this folder**

## Step 8: Generate Slides (if requested)

If user chose option B or C:

1. Identify the 5-8 most important frameworks/concepts from the KB
2. **Report to user**: "I'll create a slide deck with {N} slides covering: [list]. OK?"
3. After confirmation, use `baoyu-slide-deck` or `pptx-generator` to create presentation
4. Save to `domains/{slug}/published/`

## Step 9: Verify & Present

1. List all generated files:

```
📁 domains/{slug}/
├── published/                          ← DOCX ONLY, user imports this folder
│   ├── 00-学习路径（先读这个）.docx
│   ├── 00-术语表（随查随用）.docx
│   ├── 01-欧盟AI法案（EU AI Act）要点.docx
│   ├── 02-xxx.docx
│   ├── ...
│   ├── 附-知识骨架.docx
│   └── 附-核心研究者.docx
├── assets/                             ← images, scripts, intermediate files
│   ├── hcai-framework.png
│   └── ...
├── convert_to_docx.py                  ← conversion script
└── slides/                             ← if slides were generated
    └── AI产品经理-知识分享.pptx
```

**Key checks**:
1. `published/` contains **only** `.docx` files — no scripts, images, or subdirectories
2. Files sort correctly by name — `00` first, `01-09` in learning order, `附` last
3. All file names are Chinese-first (bilingual for terms without standard Chinese names)

2. Update `meta.md`: mark Phase 5 complete
3. Tell user: "Files ready. Import DOCX into Feishu, or use the slides for team sharing."

## Cost Control Summary

| Action | Needs Confirmation? |
|--------|-------------------|
| MD → text-only DOCX | No (free, just formatting) |
| Image generation | **YES — must show count + list + wait** |
| Slide deck creation | Yes (confirm scope) |
