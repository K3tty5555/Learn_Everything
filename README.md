# Learn Everything

Systematically learn any domain using Claude Code — from finding the top researchers to generating a personalized study plan.

用 Claude Code 系统性地学习任何领域 — 从找到顶级研究者到生成个性化学习路径。

## How It Works | 工作原理

```
/learn quantum-computing
```

One command kicks off a 5-phase pipeline:

| Phase | Command | What It Does |
|-------|---------|-------------|
| 1 | `/learn-skeleton` | Find top 3-5 researchers + 10 most-cited papers |
| 2 | `/learn-kb` | Build sourced knowledge base (parallel agents) |
| 3 | `/learn-challenge` | Multi-expert adversarial Q&A to expose blind spots |
| 4 | `/learn-path` | Generate personalized learning roadmap |
| 5 | `/learn-publish` | Export to DOCX (Feishu-ready) with infographics |

Plus two study tools:
- `/learn-quiz` — Test your understanding with generated quizzes
- `/learn-tutor` — Socratic tutor mode (Feynman technique)

一条命令启动 5 阶段学习流水线：

| 阶段 | 命令 | 功能 |
|------|------|------|
| 1 | `/learn-skeleton` | 找到领域 Top 3-5 研究者 + 10 篇最高引用论文 |
| 2 | `/learn-kb` | 并行构建有来源标注的知识库（零容忍幻觉） |
| 3 | `/learn-challenge` | 多专家对抗式提问，暴露知识盲区 |
| 4 | `/learn-path` | 生成个性化学习路线图 |
| 5 | `/learn-publish` | 导出为 DOCX（可直接导入飞书）+ 信息图 |

额外学习工具：
- `/learn-quiz` — 生成测验题检验理解程度
- `/learn-tutor` — 苏格拉底式辅导（费曼学习法）

## Quick Start | 快速开始

### Prerequisites | 前置条件

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed and configured
- 已安装并配置 [Claude Code](https://docs.anthropic.com/en/docs/claude-code)

### Usage | 使用

```bash
# Clone the repo
git clone git@github.com:K3tty5555/Learn_Everything.git
cd Learn_Everything

# Start learning any topic
claude
> /learn quantum-computing
```

That's it. Claude will guide you through each phase.

就这么简单。Claude 会引导你完成每个阶段。

### Image Generation (Optional) | 图片生成（可选）

Phase 5 (`/learn-publish`) can generate infographics for your DOCX files. This requires an API key for at least one image provider (OpenAI, Google, DashScope, MiniMax, Replicate, etc.).

On first use, the `baoyu-image-gen` skill will guide you through setup. You can also skip image generation entirely — text-only DOCX works fine.

第 5 阶段（`/learn-publish`）可以为 DOCX 文件生成信息图。需要至少一个图片生成服务的 API key（OpenAI、Google、DashScope、MiniMax、Replicate 等）。

首次使用时 `baoyu-image-gen` 会引导你完成配置。也可以跳过图片生成 — 纯文本 DOCX 同样可用。

## What You Get | 你会得到什么

After completing all phases, your `domains/{topic}/published/` folder contains numbered DOCX files ready to import into Feishu (or any document system):

完成所有阶段后，`domains/{topic}/published/` 文件夹包含编好号的 DOCX 文件，可直接导入飞书（或任何文档系统）：

```
published/
├── 00-学习路径（先读这个）.docx
├── 00-术语表（随查随用）.docx
├── 01-第一个主题.docx
├── 02-第二个主题.docx
├── ...
├── 附-知识骨架.docx
└── 附-核心研究者.docx
```

- `00-` = Read first / reference (先读 / 随时查)
- `01-09` = Sequential reading order (按顺序阅读)
- `附-` = Background material, optional (附录，可选阅读)

Sort by filename = learning order. No separate index needed.

按文件名排序 = 学习顺序。不需要单独的目录。

## Design Principles | 设计原则

- **Zero hallucination**: Every claim carries a source `[Source: Author, Year]`
- **Chinese-first content**: All generated content in Chinese, bilingual for terms without standard translations
- **Parallel execution**: Paper analysis, adversarial review, and DOCX generation run as parallel agents
- **Cost-controlled images**: Never generates images without explicit user confirmation
- **Self-contained**: All required skills bundled in `.claude/skills/`, clone and use

---

- **零幻觉**：每条论断都标注来源 `[Source: Author, Year]`
- **中文优先**：所有生成内容使用中文，无标准译名的术语用双语格式
- **并行执行**：论文分析、对抗审查、DOCX 生成均以并行 Agent 运行
- **成本可控**：生成图片前必须用户确认
- **自包含**：所有依赖 skill 已打包在 `.claude/skills/`，clone 即用

## Project Structure | 项目结构

```
Learn_Everything/
├── CLAUDE.md              # Project instructions for Claude Code
├── .claude/
│   ├── commands/          # Slash command definitions (/learn, /learn-kb, etc.)
│   └── skills/            # Bundled skills (minimax-docx, baoyu-infographic, etc.)
├── templates/             # Shared templates for knowledge notes
└── domains/               # Your learning data (gitignored, personal)
    └── {topic}/
        ├── skeleton.md
        ├── researchers.md
        ├── papers/
        ├── concepts/
        ├── learning-path.md
        └── published/     # Final DOCX output
```

## License | 许可

MIT
