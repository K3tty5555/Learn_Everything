---
description: "Phase 2 of Learn Everything: Build comprehensive learning modules in parallel. Each module is a complete, readable learning document (1500-3000 words). Zero hallucination tolerance — every claim has a source. Use when user says /learn-kb or wants to build the knowledge base for a domain."
---

# Learn Everything — Phase 2: 模块化知识库构建

**Domain**: $ARGUMENTS

## Goal

以模块为单位，并行构建完整的学习内容。每个模块是一份可以独立阅读的学习文档（1500-3000字），覆盖该模块的所有重要知识点。来源（论文、博客、文档）是证据，不是结构主体。

## Step 1: Verify & Load

1. 读取 `domains/{slug}/meta.md` — Phase 1 必须完成
2. 读取 `domains/{slug}/curriculum.md` — 获取模块清单和学习目标
3. 读取 `domains/{slug}/sources.md` — 获取来源清单及 source→module 映射

**ISOLATION RULE**: 只访问 `domains/{slug}/` 和 `templates/`。

## Step 2: 来源准备

询问用户：
- 来源清单中哪些书籍/付费内容有 PDF？
- 其余来源将通过 WebSearch/WebFetch 获取公开信息

在 sources.md 中标注每个来源的可用程度：
- `[全文可用]` — 有 PDF 或开放全文
- `[公开信息]` — 只有摘要/评论/作者访谈
- `[网页可获取]` — 可用 WebFetch 抓取

## Step 3: 并行模块构建 — 核心步骤

**按模块启动并行 Agent，每个 Agent 负责 1-2 个模块。**

每个 Agent 接收：
- 该模块的名称、核心问题、深度要求（来自 curriculum.md）
- 该模块对应的来源列表（来自 sources.md 的 source→module 映射）
- `templates/module.md` 模板结构
- 以下构建指令：

---
*Agent 指令（每个模块 Agent 都收到此指令）：*

你负责构建模块 {N}：{模块名}，核心问题：{核心问题}

**你的任务：**
1. 用 WebSearch/WebFetch 研究该模块对应的来源
2. 综合多来源（学术 + 实践 + 技术文档），撰写 1500-3000 字的完整模块文档
3. 遵循 `templates/module.md` 的结构（为什么必须懂这个 / 核心内容 / 实战场景 / 决策框架 / 常见误区 / 来源 / 延伸阅读）
4. **每个关键 claim 必须标注来源**：`[Source: Author/Platform, Year/Date, Section]`
5. 无法验证的信息标注 `[Unverified]`
6. 只用公开信息的来源标注 `[Based on public information only]`
7. 写入 `domains/{slug}/modules/{NN}-{slug}.md`

**写作要求：**
- 全程用中文，术语用双语格式：中文（English）
- 深度优先：宁可把重要小节写透，不要面面俱到却每处都浅
- 实战场景必须具体：给出真实的产品/决策场景，不是泛泛而谈
- 决策框架必须可操作：PM 遇到什么情况时用这个知识做什么决定
---

**示例分批方式（10个模块）：**
- Agent 1：模块01, 02
- Agent 2：模块03, 04
- Agent 3：模块05, 06
- Agent 4：模块07, 08
- Agent 5：模块09, 10

**注意：modules/ 目录需先创建：** `domains/{slug}/modules/`

## Step 4: 质量检查

所有模块完成后，逐一检查：
- [ ] 字数达到 1500 字以上
- [ ] 有「实战场景」小节，且场景具体
- [ ] 有「决策框架」小节，且可操作
- [ ] 每个关键 claim 有来源标注
- [ ] `[Unverified]` 不超过 3 处（超过则补充研究）

对不达标的模块，重新补充研究并更新。

## Step 5: Update & Present

1. 更新 `meta.md`：标记 Phase 2 完成，记录模块数量和总字数（估算）
2. 更新 `domains/index.md`
3. 向用户汇报：
   - 已构建模块数及列表
   - 各模块来源类型覆盖情况
   - 需要用户补充 PDF 才能提升质量的来源（如有）
4. 建议："准备好进入 Phase 3 深度挑战了吗？运行 `/learn-challenge {topic}`"
