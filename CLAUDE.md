# Learn Everything

A systematic domain learning system powered by Claude Code.

## Methodology

1. **Knowledge Skeleton** - 判断领域知识形态（学术/实践/技术文档/混合），设计 8-12 个学习模块，按类型发现来源（论文/书籍/博客/官方文档）
2. **Knowledge Base** - 以模块为单位并行构建完整学习文档（1500-3000字/模块），来源是证据而非结构主体，zero hallucination tolerance
3. **Adversarial Learning** - Multi-agent critical questioning to fill blind spots
4. **Learning Path** - Layered roadmap from foundation to frontier

## Project Conventions

- All knowledge is organized under `domains/{domain-slug}/`
- **Domain Isolation**: When working on domain X, ONLY access files in `domains/{x}/` and `templates/`. Never read other domain directories.
- `domains/index.md` is a lightweight registry (one line per domain). It is the ONLY cross-domain file.
- `domains/{slug}/curriculum.md` — P1 产出：课程大纲、模块清单、知识形态分类
- `domains/{slug}/sources.md` — P1 产出：按学术/实践/技术文档分类的来源清单（含 source→module 映射）
- `domains/{slug}/modules/` — P2 核心产出：模块化学习文档（每模块 1500-3000 字，含实战场景和决策框架）
- Every factual claim must carry a source annotation: `[Source: Author et al., Year, Section/Page]`
- Unverified information must be marked `[Unverified]`
- Templates in `templates/` are shared and read-only references

## Content Language — Chinese First

All generated content (MD files, DOCX content, headings, section titles) MUST use **Chinese as the primary language**. This applies to every phase (skeleton, KB, learning path, publish).

- Section headings: Chinese, not English (e.g., `## 你的起点` not `## Your Starting Position`)
- Technical terms without standard Chinese translations: use bilingual format `中文（English）` (e.g., `机器学习技术债务（ML Technical Debt）`)
- Universal acronyms (AI, ML, PM, API) can be used directly without translation
- Source annotations remain in original language: `[Source: Author et al., Year]`

## Image Generation Cost Control

**GLOBAL RULE: Never generate images without explicit user confirmation.**

Before calling any image generation skill (baoyu-infographic, baoyu-image-gen, baoyu-article-illustrator, baoyu-imagine, minimax-multimodal-toolkit), you MUST:
1. Tell the user exactly how many images you plan to generate
2. List what each image will contain
3. Wait for explicit confirmation (Y / N / pick specific ones)

This rule applies across ALL skills in this project, not just learn-publish.

## Available Skills

| Command | Purpose |
|---------|---------|
| `/learn {topic}` | Entry point: initialize domain + route |
| `/learn-skeleton {topic}` | Phase 1: Find researchers & papers |
| `/learn-kb {topic}` | Phase 2: Build sourced knowledge base (parallel) |
| `/learn-challenge {topic}` | Phase 3: Multi-expert adversarial Q&A (parallel) |
| `/learn-path {topic}` | Phase 4: Personalized learning path |
| `/learn-publish {topic}` | Phase 5: Export to DOCX/slides with images |
| `/learn-quiz {topic}` | Test your understanding with generated quizzes |
| `/learn-tutor {topic}` | Socratic tutor mode (Feynman technique) |

## Bundled Skills

The following external skills are bundled in `.claude/skills/` for self-contained distribution:

| Skill | Purpose | Source |
|-------|---------|--------|
| `minimax-docx` | DOCX generation with OpenXML (tables, CJK typography, design principles) | MiniMaxSkills |
| `baoyu-infographic` | Infographic image generation (21 layouts, 20 styles) | baoyu-skills |
| `baoyu-image-gen` | AI image generation backend (OpenAI, Google, DashScope, MiniMax, Replicate, etc.) | baoyu-skills |
| `Humanizer-zh` | De-AI text processing (remove AI writing patterns) | baoyu-skills |

These skills are invoked by name during `/learn-publish`. No external installation required.

**Image generation setup**: `baoyu-image-gen` needs an API key for at least one image provider. On first use, the skill will guide users through provider selection and API key configuration. Keys are stored in environment variables or `~/.baoyu-skills/.env` (never committed to git).

## File Naming

- Domain slugs: lowercase, hyphens, no spaces (e.g., `quantum-computing`)
- Paper slugs: `{first-author-year}` (e.g., `vaswani-2017`)
- Concept files: lowercase, hyphens (e.g., `attention-mechanism.md`)
- QA logs: `{YYYY-MM-DD}.md`
