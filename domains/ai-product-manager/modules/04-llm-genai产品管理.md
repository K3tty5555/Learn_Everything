---
module: "04"
title: LLM/GenAI 产品管理
domain: ai-product-manager
sources:
  - Nika-Building-2025
  - Shi-Reimagined-2024
  - OpenAI-Evals-Docs
last_updated: 2026-03-31
---

# 模块04：LLM/GenAI 产品管理

## 为什么必须懂这个

生成式 AI（Generative AI）产品管理是当前 AI PM 最核心的专项能力。会用 ChatGPT 和会构建 LLM 产品是截然不同的两件事：前者是消费者，后者需要理解提示词工程、检索增强生成、模型评估、成本管理的完整链条。

在智学网的场景下，几乎所有新 AI 功能都会涉及 LLM——无论是作文批改、解题辅导、学情分析报告生成，还是对话式学习助手。如果你不懂 LLM 产品的特殊规律，你将无法做出正确的技术选型决策、无法设计有效的评估体系、无法与工程师对等协作。

## 核心内容

### 提示词工程（Prompt Engineering）：PM 视角

提示词工程是通过设计输入文本来控制 LLM 输出行为的方法论。对 PM 而言，理解提示词工程的价值在于：许多看似需要"重新训练模型"的问题，实际上通过精心设计提示词就能解决，成本差异可能是千倍。

**核心提示词模式**：

**零样本（Zero-shot）**：直接描述任务，不给示例。
```
批改以下作文，从内容、语言、结构三个维度给出评价和建议：
[作文内容]
```
适用场景：通用任务，LLM 已有足够训练数据覆盖。

**少样本（Few-shot）**：给出2-5个输入/输出示例，让模型理解期望格式。
```
示例1 - 输入：[作文A] → 输出：{内容:B+, 语言:A-, 建议:...}
示例2 - 输入：[作文B] → 输出：{内容:A, 语言:B, 建议:...}
现在批改：[作文C]
```
适用场景：有特定格式要求或特定评判标准的任务。结构化输出质量显著提升。

**思维链（Chain-of-Thought, CoT）**：要求模型展示推理过程，再给出结论。
```
请一步一步分析这道数学题的解题思路，然后给出答案。
```
适用场景：需要推理的任务（数学、逻辑分析）。减少"快速猜测"导致的错误。

**系统提示词（System Prompt）**：在对话开始前设定 AI 的角色、行为准则和约束条件。
```
你是智学网的数学辅导助手"小学"。你的角色是：
1. 引导学生思考，而不是直接给出答案
2. 使用适合初中生的语言
3. 当学生提出与学习无关的问题时，礼貌引导回学习话题
4. 对于自己不确定的内容，明确说"我不确定，建议咨询老师"
```

**PM 提示词管理责任**：提示词不是一次性设置，而是需要版本管理、A/B 测试、持续优化的产品资产。PM 需要建立提示词的变更管理流程，确保每次修改都有评估对比。

[Source: Nika, Marily. Building AI-Powered Products. O'Reilly, 2025, Chapter 5]

### RAG vs Fine-tuning：最重要的技术选型决策

这是 LLM 产品 PM 最常面临的核心技术架构决策，必须能独立判断。

**检索增强生成（Retrieval-Augmented Generation, RAG）**
将外部知识库（如题库、教材内容、历史作答记录）实时检索并注入提示词，让 LLM 基于最新的、特定的知识生成答案。

**微调（Fine-tuning）**
在基础 LLM 上，用特定领域数据继续训练，让模型学习特定的风格、知识或行为模式。

**决策矩阵**：

| 因素 | 优先选 RAG | 优先选 Fine-tuning |
|------|-----------|------------------|
| 知识更新频率 | 频繁（每天/每周更新题库） | 稳定（固定教学风格） |
| 知识量 | 大规模（数十万道题） | 较小但密集（特定学科专家知识） |
| 准确性要求 | 需要精确引用来源 | 需要特定输出格式或风格 |
| 成本 | 检索成本 + 较长上下文成本 | 一次性训练成本 + 较低推理成本 |
| 可解释性 | 高（可看到引用了哪些内容） | 低（知识编码在权重中） |
| 时效性 | 即时生效（更新知识库即可） | 需要重新训练（周/月级别周期） |

**智学网场景推荐**：
- 题目解析辅导 → **RAG**（需要精确对应特定题目，题库随时更新）
- 教师批改风格模仿 → **Fine-tuning**（学习特定教师的评语风格，数据稳定）
- 学情分析报告生成 → **RAG**（需要引用具体学习数据）
- 对话辅导助手人格塑造 → **Fine-tuning**（统一助手的语气和教学风格）

[Source: Shi, Shyvee et al. Reimagined: Building Products with Generative AI, 2024, Chapter 4]

### LLM Evals（评估体系）设计：PM 的核心职责

LLM 产品的评估体系设计是 PM 最重要但最容易被忽视的职责。没有严格的 Evals，你无法判断一次提示词修改是否真的提升了质量，也无法知道模型升级是否引入了新问题。

**Evals 的三个层次**：

**层次1：单元级评估（Unit Evals）**
针对具体输出的质量评估。
- 方式：准备一批"黄金测试集"（Golden Test Set），包含输入和期望输出，定期运行对比。
- 指标示例：作文批改准确率、解题步骤正确率、格式合规率。

**层次2：行为级评估（Behavioral Evals）**
评估模型在特定情境下的行为是否符合预期。
- 安全性测试：用户输入违规内容时，AI 是否正确拒绝？
- 边界测试：作业超出题库范围时，AI 是否诚实承认不知道？
- 一致性测试：同一问题不同表达方式，AI 是否给出一致答案？

**层次3：端到端评估（End-to-End Evals）**
评估完整对话或任务流的整体质量。
- 方式：模拟真实用户会话，评估学生从困惑到理解的完整辅导效果。
- 指标示例：任务完成率（学生最终理解了知识点吗？）、对话轮次（花了多少轮才解决问题？）。

**评估者的类型**：
- **自动评估器**：规则匹配（答案是否包含必要步骤）、模型评估（用另一个 LLM 评判输出质量）
- **人工评估**：领域专家（数学老师）评判解题解析质量
- **用户隐性反馈**：用户在 AI 给出答案后是否继续提问？是否反馈"这个不对"？

[Source: OpenAI, Evaluation Best Practices. https://platform.openai.com/docs/guides/evaluation-best-practices]

**PM 的 Evals 责任清单**：
1. 在功能立项时定义评估指标和通过标准
2. 维护测试集的更新（随着产品迭代，测试集需要扩充）
3. 在每次重大提示词变更或模型版本升级前运行 Evals
4. 建立 Evals 指标的监控看板，设置性能退化的告警阈值

### 关键 LLM 参数：PM 需要掌握的产品参数

**Temperature（温度）**：控制输出随机性。
- 0-0.3：确定性高，适合需要准确性的场景（解题步骤、事实问答）
- 0.7-1.0：多样性高，适合创意场景（写作灵感、头脑风暴）
- PM 职责：为每个功能场景明确规定 temperature 范围，防止"创意模式"用在需要精确答案的场景。

**Max Tokens（最大输出长度）**：控制 AI 响应的最大长度。
- 设置太低：答案被截断，体验差
- 设置太高：成本增加，响应慢
- PM 职责：根据场景定义合理上限（单题解析 vs 完整学情报告的上限不同）

**Top-p / Top-k**：控制采样范围，影响输出多样性。通常与 temperature 联动调整，PM 可以交给工程师管理，但需要理解其效果。

**上下文窗口（Context Window）管理**：
- 长对话历史会占用上下文窗口，影响成本和性能
- PM 需要设计"记忆策略"：保留多少轮对话历史？哪些信息需要持久化？哪些可以丢弃？

[Source: Bratsis, Irene. The AI Product Manager's Handbook. Packt, 2023, Chapter 5]

### GenAI 产品的独特 PRD 要求

传统 PRD 关注"功能列表"，LLM 功能的 PRD 需要额外包含：

1. **Prompt 规格**：系统提示词的设计意图和关键约束
2. **Evals 规格**：功能上线前必须通过的评估指标和阈值
3. **降级（Fallback）规格**：当 AI 无法完成任务时的备选路径
4. **错误分类**：哪些错误是可接受的（格式问题）？哪些是不可接受的（事实错误、有害内容）？
5. **成本预算**：每次调用的 token 预算，避免失控的推理成本

## 实战场景

**场景：设计智学网"AI解题辅导"功能的技术规格**

功能目标：学生拍照上传数学题，AI 提供分步解题辅导（引导式，不直接给答案）。

**技术选型决策**：
- 采用 RAG + 题库对照（确保解题方法与教材一致）
- 不使用 Fine-tuning（题目类型过多，维护成本高）
- Temperature = 0.1（数学解题需要确定性，不需要创意性）

**Prompt 设计要点**：
```
系统提示：你是一位苏格拉底式数学辅导老师。
规则：
1. 永远不要直接给出最终答案
2. 每次只引导学生思考下一步
3. 如果学生卡住超过3次，提供更明确的提示
4. 解题步骤必须与教材方法一致（参考检索到的题库解析）
```

**Evals 设计**：
- 黄金测试集：100道覆盖主要题型的题目，含标准引导对话
- 通过标准：引导准确率 > 90%，步骤合规率 > 95%，有害内容率 = 0%
- 运行频率：每次 Prompt 修改后，每次模型版本升级前

## 决策框架

**LLM 功能立项的技术可行性评估**

```
Step 1：能否用规则/传统 ML 解决？
  如果是 → 不用 LLM（更简单、更可控、更便宜）

Step 2：RAG vs Fine-tuning（参照上方决策矩阵）

Step 3：Prompt 工程是否足够？
  评估：Zero-shot / Few-shot / CoT 的效果
  如果效果达标 → 进入 Evals 设计阶段

Step 4：Evals 设计
  定义：指标、阈值、测试集、运行频率

Step 5：成本计算
  估算：每日调用量 × 平均 token 数 × token 单价 = 日成本
  验证：成本是否在业务可接受范围内
```

## 常见误区

- **误区1：提示词写得越详细越好** → 正确理解：过度复杂的提示词会增加 token 成本，且可能引入矛盾指令。关键是精准，而非冗长。每条约束都应该有明确的目的，无目的的条款应删除。

- **误区2：用同一套 Evals 评估所有 LLM 功能** → 正确理解：不同功能的成功标准截然不同。解题辅导看步骤准确率，作文批改看维度覆盖率，情感支持对话看安全性和共情度。每个功能需要定制化的 Evals。

- **误区3：Fine-tuning 总是比 RAG 更准确** → 正确理解：Fine-tuning 在特定任务上可能更高效，但对于知识密集型任务，RAG 因为能动态检索最新内容，通常更准确且更易维护。[Source: Shi et al., Reimagined, 2024]

- **误区4：模型版本越新越好，应该始终用最新模型** → 正确理解：新模型版本可能在某些方面更好，但也可能在你的特定任务上退步（模型回归问题）。每次模型版本升级都必须先在 Evals 测试集上验证，再决定是否切换。[Source: OpenAI Evals Framework, GitHub]

## 来源

- [Source: Nika, Marily. Building AI-Powered Products. O'Reilly, 2025.]
- [Source: Shi, Shyvee; Cai, Caitlin; Rong, Yiwen. Reimagined: Building Products with Generative AI, 2024.]
- [Source: OpenAI. Evaluation Best Practices. https://platform.openai.com/docs/guides/evaluation-best-practices]
- [Source: OpenAI. Working with Evals. https://platform.openai.com/docs/guides/evals]
- [Source: Bratsis, Irene. The AI Product Manager's Handbook. Packt, 2023.]
- [Source: Noy & Zhang. Experimental evidence on productivity effects of generative AI. Science, 2023.]

## 延伸阅读

- OpenAI Evals Framework（GitHub）：https://github.com/openai/evals
- Anthropic Claude Docs — Overview：https://docs.anthropic.com/en/docs/overview
- Lenny's Newsletter — AI for PMs 系列：https://www.lennysnewsletter.com
- Maven — AI Product Management 101（Marily Nika）：https://maven.com/marily-nika/ai-product-management
