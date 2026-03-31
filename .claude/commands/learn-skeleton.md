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

**LANGUAGE RULE**: 模块名和核心问题用中文，技术术语用双语格式：中文（English）。

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
   - 来源清单概览（按类型分组）
4. 询问："大纲方向正确吗？需要调整哪些模块？确认后运行 `/learn-kb {topic}`"
