# Module-First Architecture 实施计划

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** 将 Learn Everything 从「论文笔记档案库」重构为「模块化学习系统」，让用户读完 published/ 后真正掌握该领域的核心知识。

**Architecture:** P1 新增领域知识形态判断 + 课程大纲设计；P2 改为按模块并行构建（论文降级为来源）；P3/P4 适配新目录结构；新增三个模板文件。

**Tech Stack:** Markdown skill files（`.claude/commands/`），无代码变更，全部是 prompt 改写。

---

## Task 1：新增三个模板文件

**Files:**
- Create: `templates/curriculum.md`
- Create: `templates/sources.md`
- Create: `templates/module.md`

**Step 1: 创建 `templates/curriculum.md`**

```markdown
---
domain: {Domain Name}
slug: {domain-slug}
created: {YYYY-MM-DD}
knowledge_type: academic|practitioner|technical|mixed
---

# {Domain Name} — 课程大纲

## 领域知识形态
- **类型：** {academic / practitioner / technical / mixed}
- **来源策略：** {各类型来源的比例与重点，1-2句}

## 模块清单

| # | 模块名 | 核心问题 | 深度要求 | 前置模块 |
|---|--------|---------|---------|---------|
| 01 | {模块名} | {学完这个模块，我能做什么} | 概览/掌握/精通 | 无 |
| 02 | ... | ... | ... | 01 |

## 模块依赖关系

{用文字或简单列表说明学习顺序}
```

**Step 2: 创建 `templates/sources.md`**

```markdown
---
domain: {Domain Name}
slug: {domain-slug}
---

# {Domain Name} — 来源清单

## 学术来源
| 研究者/论文 | 类型 | 覆盖模块 | 获取方式 |
|-----------|------|---------|---------|
| {Author et al., Year — Title} | 论文/书籍 | 模块03, 06 | Open access / 需PDF |

## 实践来源
| 来源 | 类型 | 覆盖模块 | URL/备注 |
|-----|------|---------|---------|
| {作者 — 文章/书名} | 博客/书籍/案例 | 模块04 | {url} |

## 技术文档来源
| 来源 | 类型 | 覆盖模块 | URL |
|-----|------|---------|-----|
| {平台 — 文档名} | 官方文档/工程博客 | 模块04, 05 | {url} |
```

**Step 3: 创建 `templates/module.md`**

```markdown
---
module: {NN}
title: {模块名}
domain: {domain-slug}
sources: []
last_updated: {YYYY-MM-DD}
---

# 模块{NN}：{模块名}

## 为什么 PM 必须懂这个

{1-2段，建立学习动机。回答：不懂这个会有什么后果？}

## 核心内容

### {小节1}

{深度叙述，有具体解释、例子}
[Source: ...]

### {小节2}

...

## 实战场景

{结合用户具体场景的案例（如智学网/当前项目）}

## 决策框架

{这个概念如何影响 PM 的日常工作和关键决策}

## 常见误区

- **误区1：** {描述} → {正确理解}
- **误区2：** ...

## 来源

- [Source: ...]

## 延伸阅读

- {如需深入，看这些}
```

**Step 4: 验证三个文件存在**

```bash
ls templates/
```
预期输出中包含：`curriculum.md`、`sources.md`、`module.md`

**Step 5: Commit**

```bash
git add templates/curriculum.md templates/sources.md templates/module.md
git commit -m "feat: add curriculum, sources, module templates for module-first architecture"
```

---

## Task 2：重写 learn-skeleton.md（P1 完全重写）

**Files:**
- Modify: `.claude/commands/learn-skeleton.md`

**Step 1: 完整替换文件内容**

```markdown
---
description: "Phase 1 of Learn Everything: Analyze domain knowledge type, design curriculum (8-12 modules), and discover sources by category. Use when user says /learn-skeleton or needs to build the foundational structure of a domain."
---

# Learn Everything — Phase 1: 领域分析 + 课程设计

**Domain**: $ARGUMENTS

## Goal

通过三步分析，为该领域建立学习骨架：判断知识形态 → 设计课程大纲 → 按类型发现来源。
这是所有后续阶段的基础。

## Step 1: Verify Domain

1. 将 topic 规范化为 slug
2. 读取 `domains/{slug}/meta.md` — 如果 Phase 1 已完成，询问用户是否重做
3. 如果域不存在，告知用户先运行 `/learn {topic}`

**ISOLATION RULE**: 只访问 `domains/{slug}/` 和 `templates/`。

## Step 2: 领域知识形态判断

**并行搜索**以下内容：
- `"{topic} best books practitioners"`
- `"{topic} top researchers academic papers"`
- `"{topic} official documentation guide"`
- `"{topic} leading blogs practitioners 2024 2025"`
- Semantic Scholar: `https://api.semanticscholar.org/graph/v1/paper/search?query={topic}&limit=10&fields=title,authors,year,citationCount`

基于搜索结果，判断该领域的**知识形态**：

| 形态 | 判断标准 | 来源策略 |
|------|---------|---------|
| **学术型** | 高引论文多，有活跃研究社区 | 80% 学术，20% 实践 |
| **实践型** | 从业者书籍/博客是主要知识载体 | 20% 学术，80% 实践 |
| **技术文档型** | 官方文档/工程博客是权威来源 | 30% 学术，70% 技术文档 |
| **混合型** | 多种形态并存 | 按实际比例分配 |

## Step 3: 课程大纲设计

基于领域知识全貌，设计 **8-12 个学习模块**：

- 每个模块对应一个「学习者需要能做到的事」（而非「一篇论文说了什么」）
- 考虑模块间的依赖关系（哪些需要先学）
- 为每个模块评估深度要求：概览 / 掌握 / 精通

输出 `domains/{slug}/curriculum.md`（使用 `templates/curriculum.md`）

## Step 4: 按类型发现来源

**并行搜索**三类来源：

**学术来源**（如适用）：
- 高引论文、综述论文、顶尖研究者

**实践来源**（如适用）：
- 畅销书籍、知名从业者博客、行业案例研究、播客

**技术文档来源**（如适用）：
- 官方文档、工程博客、GitHub 优质项目

**每个来源必须标注：覆盖哪些模块**（建立 source → module 映射）

输出 `domains/{slug}/sources.md`（使用 `templates/sources.md`）

## Step 5: Update & Present

1. 更新 `meta.md`：标记 Phase 1 完成，记录知识形态类型
2. 更新 `domains/index.md`
3. 向用户展示：
   - 领域知识形态判断结果
   - 课程大纲（模块列表 + 学习目标）
   - 来源清单概览（按类型）
4. 询问："大纲方向正确吗？需要调整哪些模块？确认后运行 `/learn-kb {topic}`"
```

**Step 2: 验证文件**

读取 `.claude/commands/learn-skeleton.md` 确认内容正确。

**Step 3: Commit**

```bash
git add .claude/commands/learn-skeleton.md
git commit -m "feat: rewrite learn-skeleton with domain-type analysis and curriculum design"
```

---

## Task 3：重写 learn-kb.md（P2 完全重写）

**Files:**
- Modify: `.claude/commands/learn-kb.md`

**Step 1: 完整替换文件内容**

```markdown
---
description: "Phase 2 of Learn Everything: Build comprehensive learning modules in parallel. Each module is a complete, readable learning document. Zero hallucination tolerance — every claim has a source. Use when user says /learn-kb or wants to build the knowledge base for a domain."
---

# Learn Everything — Phase 2: 模块化知识库构建

**Domain**: $ARGUMENTS

## Goal

以模块为单位，并行构建完整的学习内容。每个模块是一份可以独立阅读的学习文档（1500-3000字），覆盖该模块的所有重要知识点。论文和博客是来源，不是结构主体。

## Step 1: Verify & Load

1. 读取 `domains/{slug}/meta.md` — Phase 1 必须完成
2. 读取 `domains/{slug}/curriculum.md` — 获取模块清单
3. 读取 `domains/{slug}/sources.md` — 获取来源清单及 source→module 映射

**ISOLATION RULE**: 只访问 `domains/{slug}/` 和 `templates/`。

## Step 2: 来源准备

询问用户：
- 来源清单中哪些书籍/付费内容有 PDF？
- 其余来源将通过 WebSearch/WebFetch 获取公开信息

标注每个来源的可用程度：
- `[全文可用]` — 有 PDF 或开放全文
- `[公开信息]` — 只有摘要/评论/作者访谈
- `[网页可获取]` — 可用 WebFetch 抓取

## Step 3: 并行模块构建 — 核心步骤

**按模块启动并行 Agent，每个 Agent 负责 1-2 个模块。**

每个 Agent 接收：
- 该模块的名称、核心问题、深度要求
- 该模块对应的来源列表（从 sources.md 中的映射）
- `templates/module.md` 模板
- 以下构建指令：

---
*Agent 指令：*

你负责构建模块 {N}：{模块名}

**你的任务：**
1. 用 WebSearch/WebFetch 研究该模块对应的来源
2. 综合多来源，撰写 1500-3000 字的完整模块文档
3. 遵循 `templates/module.md` 的结构
4. **每个 claim 必须标注来源**：`[Source: Author/Platform, Year/Date, Section]`
5. 无法验证的信息标注 `[Unverified]`
6. 只用公开信息的来源标注 `[Based on public information only]`
7. 写入 `domains/{slug}/modules/{NN}-{slug}.md`

**写作要求：**
- 用中文写作（术语用双语格式：中文（English））
- 深度优先：宁可少模块写透，不要多模块写浅
- 实战场景：必须有结合用户实际场景的具体案例
- 决策框架：必须说明 PM 如何用这个知识做决策
---

**示例分批方式（10个模块）：**
- Agent 1：模块01, 02
- Agent 2：模块03, 04
- Agent 3：模块05, 06
- Agent 4：模块07, 08
- Agent 5：模块09, 10

## Step 4: 质量检查

所有模块完成后，检查每个模块：
- [ ] 字数达到 1500 字以上
- [ ] 有「实战场景」小节
- [ ] 有「决策框架」小节
- [ ] 每个关键 claim 有来源标注
- [ ] 没有出现「[Unverified]」过多的情况（超过3处需补充研究）

对不达标的模块，重新补充研究。

## Step 5: Update & Present

1. 更新 `meta.md`：标记 Phase 2 完成，记录模块数量
2. 更新 `domains/index.md`
3. 汇报：已构建模块数、总字数、来源覆盖情况、需要用户补充 PDF 的来源
4. 建议："准备好进入 Phase 3 深度挑战了吗？运行 `/learn-challenge {topic}`"
```

**Step 2: 验证文件**

读取 `.claude/commands/learn-kb.md` 确认内容正确。

**Step 3: Commit**

```bash
git add .claude/commands/learn-kb.md
git commit -m "feat: rewrite learn-kb as module-first parallel builder"
```

---

## Task 4：更新 learn-challenge.md（P3 适配）

**Files:**
- Modify: `.claude/commands/learn-challenge.md`

**Step 1: 修改 Step 1（Verify & Load）**

将原来的：
```
2. Read `skeleton.md` and `researchers.md`
3. Scan all files in `papers/` and `concepts/`
```

改为：
```
2. 读取 `curriculum.md` 获取模块清单
3. 读取 `modules/` 下所有模块文件
```

**Step 2: 修改 Step 3（Parallel Adversarial Agents）**

在 Agent 指令中增加：
- 指定 Agent 重点挑战哪些**模块**（而非整个 domain）
- 要求 Agent 输出盲点时标明「属于哪个模块」

**Step 3: 修改 Step 4（Synthesize & Research）**

将「Create new concept files in `concepts/`」改为：
「将新发现的内容**直接补充到对应模块文件**的相关小节中」

**Step 4: Commit**

```bash
git add .claude/commands/learn-challenge.md
git commit -m "feat: update learn-challenge to work with modules/ directory"
```

---

## Task 5：更新 learn-path.md（P4 适配）

**Files:**
- Modify: `.claude/commands/learn-path.md`

**Step 1: 修改 Step 1（Verify & Load）**

将「Read ALL domain files: skeleton, researchers, paper notes, concepts, QA logs」改为：
「读取 `curriculum.md`、`sources.md`、`modules/` 下所有模块文件、`qa/` 日志」

**Step 2: 修改 Step 3（Generate Customized Path）**

在「每个 level 包含」中增加：
- **模块链接**：每个学习步骤直接链接到对应的 `modules/XX-xxx.md` 文件
- 不再出现「去读某某书」而没有对应模块内容的情况

**Step 3: Commit**

```bash
git add .claude/commands/learn-path.md
git commit -m "feat: update learn-path to link directly to module files"
```

---

## Task 6：更新 CLAUDE.md

**Files:**
- Modify: `CLAUDE.md`

**Step 1: 更新 Methodology 部分**

将原来的描述：
```
1. Knowledge Skeleton - Find top 3-5 researchers + 10 most-cited papers
2. Knowledge Base - Structured paper notes with full source attribution
```

更新为：
```
1. Knowledge Skeleton - 判断领域知识形态，设计 8-12 个学习模块，按类型发现来源
2. Knowledge Base - 以模块为单位并行构建完整学习文档（1500-3000字/模块）
```

**Step 2: 更新 Project Conventions 部分**

新增目录说明：
```
- `domains/{slug}/curriculum.md` — P1 产出：课程大纲 + 模块定义
- `domains/{slug}/sources.md` — P1 产出：分类来源清单
- `domains/{slug}/modules/` — P2 核心产出：模块化学习文档
```

**Step 3: Commit**

```bash
git add CLAUDE.md
git commit -m "docs: update CLAUDE.md to reflect module-first architecture"
```

---

## Task 7：用 ai-product-manager 验证（重跑 P1 + P2）

**这是验收测试，确认新架构真正可用。**

**Step 1: 重跑 P1**

运行 `/learn-skeleton ai-product-manager`

验收标准：
- [ ] 生成了 `curriculum.md`（含 8-12 个模块，Agent 独立成模块）
- [ ] 生成了 `sources.md`（含学术/实践/技术文档三类来源）
- [ ] Agent 相关来源包含：Anthropic blog、LangChain 文档等实践/技术来源

**Step 2: 重跑 P2**

运行 `/learn-kb ai-product-manager`

验收标准：
- [ ] `modules/` 目录下有 8-12 个模块文件
- [ ] Agent 模块字数 ≥ 1500 字
- [ ] Agent 模块包含「实战场景」和「决策框架」小节
- [ ] Agent 模块来源不只是学术论文，包含技术博客/文档

**Step 3: 检查 Agent 模块质量**

读取生成的 Agent 模块文件，确认：
- 解释了 Agent 的架构模式（Planning/Tool/Memory/Action）
- 说明了 PM 如何评估 Agent 产品质量
- 有智学网或类似产品的具体案例
- 有来源标注
