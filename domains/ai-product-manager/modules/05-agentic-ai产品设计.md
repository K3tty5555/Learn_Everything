---
module: "05"
title: Agentic AI 产品设计
domain: ai-product-manager
sources:
  - Anthropic-Engineering-Blog-2025
  - Bratsis-Medium-2025
  - Nika-Playbook-2025
  - Arakawa-2025
last_updated: 2026-03-31
---

# 模块05：Agentic AI 产品设计

## 为什么必须懂这个

Agentic AI 是 2025-2026 年产品管理领域最重要的新兴范式转变。如果说 ChatGPT 时代的 AI PM 挑战是"如何让 AI 说对话"，Agentic AI 时代的挑战是"如何让 AI 安全地做对事"——这是一个从语言输出到现实行动的本质跨越。

Agent 不只是回答问题，它会执行代码、调用 API、操作数据库、浏览网页、填写表单、触发工作流。一个设计不当的 Agent 不只是"说错了话"，而是"做了不该做的事"——可能是错误提交了作业、误删了学生数据、给所有家长发送了错误的通知。这种失败模式要求 PM 对产品边界、人工检查点、降级机制有完全不同于传统 AI 功能的深度思考。

在教育场景中，Agentic AI 的潜力与风险并存：一个能自主规划个性化学习路径、动态调整难度、实时干预卡壳学生的 AI 辅导 Agent，可能比任何静态推荐系统都更有效；但同时，它也需要最严格的安全边界设计，因为它服务的是未成年人，其决策直接影响学习结果甚至心理健康。

## 核心内容

### Agent 与传统 AI 功能的产品定义差异

**核心区分：自主性（Autonomy）vs 确定性（Determinism）**

传统 AI 功能遵循"查询-响应"模型：用户提问 → AI 回答 → 用户决定是否采纳。这是一个**单步骤、低风险、人类始终掌控**的模式。

Agentic AI 遵循"目标-行动"模型：用户设定目标 → Agent 自主规划步骤 → Agent 执行多步骤动作序列 → 产生真实世界影响。这是一个**多步骤、可累积风险、自主执行**的模式。

| 维度 | 传统 AI 功能 | Agentic AI |
|------|------------|-----------|
| 交互模式 | 单轮问答 | 多步任务执行 |
| 自主程度 | 无（用户决策） | 高（Agent 自主规划和行动） |
| 外部影响 | 仅输出文本/内容 | 调用工具、修改数据、触发流程 |
| 错误后果 | 用户可以忽略 | 可能已经造成真实影响 |
| 可预测性 | 高（输入-输出可测试） | 低（多步执行路径组合爆炸） |
| 失败模式 | 错误输出 | 错误行动（且可能难以回滚） |
| 用户信任要求 | 中 | 极高（需要授权 Agent 行动权限） |

[Source: Anthropic Engineering Blog. Building Agents with the Claude Agent SDK. 2025. https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk]

**Agent 的技术构成（PM 视角）**：

一个完整的 Agent 系统包含四个核心组件：
1. **推理引擎（Reasoning Engine）**：LLM，负责理解任务、规划步骤、决定下一步行动
2. **工具集（Tool Set）**：Agent 可以调用的能力集合（搜索、计算器、数据库查询、API 调用等）
3. **记忆系统（Memory）**：短期记忆（当前会话上下文）+ 长期记忆（用户历史、任务状态）
4. **执行环境（Execution Environment）**：Agent 行动的"沙箱"，定义了 Agent 能操作的范围

PM 对这四个组件的核心职责是：**定义边界**——工具集包含什么（不是越多越好）？记忆保留多久（隐私问题）？执行环境的范围（Agent 能访问什么数据？能修改什么？）

### Human-in-the-Loop 设计：何时暂停，何时确认，何时自主

Human-in-the-loop（人类介入循环）设计是 Agentic AI 产品最核心的 PM 决策。设计核心是：**并非所有步骤都需要人类批准，但所有高风险步骤必须有人类检查点**。

**三层行动分类框架**：

**自主执行层（Autonomous Execution）**：
Agent 可以在无需用户确认的情况下执行的低风险、可逆的行动。
- 示例：搜索题库资料、生成练习建议、分析学习数据
- 标准：影响小、易可逆、在用户已授权的明确范围内
- 设计要求：提供"行动日志"（用户事后可以看到 Agent 做了什么）

**确认触发层（Confirmation Required）**：
Agent 执行前需要用户明确批准的中等风险行动。
- 示例：向家长发送学情报告、调整学习计划、购买增值课程内容
- 标准：不可轻易回滚、涉及外部通知、超出预设参数范围
- 设计要求：清晰展示"Agent 将要做什么"，一键确认或取消，默认行为是等待（而非超时后自动执行）

**强制暂停层（Mandatory Pause）**：
Agent 必须停止并移交给人类的高风险场景。
- 示例：涉及学生心理健康（检测到学生表达负面情绪）、数据删除操作、涉及金融交易
- 标准：不可逆影响、超出 AI 能力边界、涉及价值判断或伦理判断
- 设计要求：清晰的"升级路径"（交给哪个人类？通过什么渠道？）+ 记录为什么暂停

[Source: Bratsis, Irene. Product Management & Agentic AI. Medium, 2025. https://irenebrat.medium.com/product-management-agentic-ai-463caedf1aec]

**Anthropic 的 Agent 设计原则**：

Anthropic 的 Claude 文档明确建议：Agent 应该"最小权限"（Minimal Footprint）——只请求完成任务所必需的最小权限，优先选择可逆行动而非不可逆行动，在不确定时主动请求人类确认而非猜测。[Source: Anthropic Claude Docs — Overview. https://docs.anthropic.com/en/docs/overview]

这对 PM 的产品设计含义：**默认保守，显式授权扩展**。Agent 初始时只有最基础的能力，用户需要主动授权更多权限（而不是默认给 Agent 全部权限，再靠限制来收缩）。

### 多 Agent 编排：PM 视角下的 Orchestrator/Subagent 分工

复杂的 AI 任务通常需要多个专业化 Agent 协作完成，而不是依赖一个"全能 Agent"。这带来了全新的产品设计挑战：如何定义 Agent 之间的分工、协作和责任边界？

**Orchestrator-Subagent 架构**：

```
用户请求
    ↓
Orchestrator Agent（规划师）
    ├── 分析任务，拆解为子任务
    ├── 决定哪个 Subagent 负责哪个子任务
    └── 汇总各 Subagent 结果，生成最终输出
         ↑
    ┌────┴────┐
Subagent A    Subagent B    Subagent C
（知识检索）  （解题计算）  （个性化建议）
```

**PM 对多 Agent 系统的核心设计职责**：

1. **任务分解清晰度**：每个 Subagent 的职责边界必须清晰，避免"职责重叠"导致重复执行或相互冲突。

2. **失败传播控制**：当一个 Subagent 失败时，整体任务如何降级？是完全失败、部分完成还是有人工介入机制？

3. **调试可见性**：多 Agent 系统的"黑盒"程度远高于单个 LLM 调用。PM 需要要求工程师提供 Agent 执行的可视化追踪（哪个 Agent 做了什么、为什么这样决策）。

4. **成本控制**：多 Agent 系统的推理成本是单 Agent 的N倍。PM 需要在功能规格中定义最大允许的 Agent 调用层数和单次任务成本上限。

5. **循环防护**：防止 Orchestrator 和 Subagent 之间形成无限循环（Agent A 要求 Agent B 的输出，Agent B 又依赖 Agent A）。需要明确的循环检测和超时机制。

[Source: Anthropic Engineering Blog. Equipping Agents for the Real World with Agent Skills. 2025. https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills]

**智学网教育场景的多 Agent 架构示例**：

```
学生学习辅导请求
    ↓
学习规划 Orchestrator
    ├── 知识状态诊断 Agent（分析学生历史数据）
    ├── 题目推荐 Agent（基于诊断结果，从题库检索）
    ├── 辅导对话 Agent（执行苏格拉底式引导对话）
    └── 进度追踪 Agent（记录本次学习成果，更新学生档案）

人工检查点：
- 诊断结果告知家长前 → 确认触发层（家长通知）
- 检测到学生情绪异常 → 强制暂停层（通知班主任）
```

### Agent 的 Evals 设计：超越传统 LLM 评估

Agent 的评估比单个 LLM 调用复杂得多，因为需要评估的是多步骤任务执行的整体效果，而不仅仅是单次输出的质量。

**Agent Evals 的四个关键指标**：

**1. 任务成功率（Task Completion Rate）**
定义：给定一个明确目标，Agent 在规定步骤数内完成的比例。
计算：完整完成任务的测试用例数 / 总测试用例数
注意：需要区分"完全成功"、"部分成功"、"失败"三种状态，不能只有二元判断。

**2. 幻觉率（Hallucination Rate）**
在 Agent 场景中，幻觉更危险——Agent 可能基于幻觉的"事实"采取错误行动（例如：错误地"记住"学生曾经做过某道题，跳过了该知识点）。
测量：在已知正确答案的测试场景中，Agent 产生错误事实陈述的比率。
区分：知识性幻觉（事实错误）vs 状态性幻觉（错误的任务状态追踪）

**3. 用户干预率（Human Intervention Rate）**
定义：在设计为"自主执行"的任务中，用户主动介入或取消 Agent 行动的比例。
用途：干预率过高 → Agent 行为不可预测，用户不信任；干预率过低 → 可能用户放弃了监督（自动化偏见风险）。
目标区间：对于高风险功能，适度的干预率（5-15%）是合理的，说明用户在适当地监督 Agent。

**4. 步骤效率（Step Efficiency）**
定义：完成任务实际使用的步骤数 vs 最优步骤数的比率。
用途：步骤过多 → 成本高、速度慢；步骤过少 → 可能跳过了必要的验证步骤。
注意：步骤效率和任务成功率之间存在权衡，不能单独优化。

[Source: Arakawa & Kitamura. Agentic AI in Product Management: A Co-Evolutionary Model. arXiv, 2025 [Unverified — 待确认正式发表状态]]

**Evals 的测试场景设计**：

Agent Evals 需要特别关注以下测试场景类型：
- **正常路径（Happy Path）**：标准任务，验证基础功能
- **边界输入（Edge Cases）**：输入不完整、格式异常、超出能力范围的请求
- **对抗性输入（Adversarial Cases）**：用户试图让 Agent 执行超出授权范围的行动（提示词注入攻击）
- **连锁失败（Cascade Failure）**：一个工具调用失败时，Agent 的行为是否合理？
- **长时间任务（Long-horizon Tasks）**：需要20步以上才能完成的复杂任务，追踪状态管理和记忆准确性

### 教育场景下 Agent 辅导员的安全边界设计

教育 AI Agent 面临的安全挑战比通用 AI Agent 更复杂，原因在于：
1. 用户是未成年人，认知发展阶段影响他们评估 AI 输出的能力
2. Agent 的影响直接关系学习结果，错误可能产生长期影响
3. 教育数据高度敏感，涉及学生隐私和家庭信息

**教育 Agent 的五层安全设计**：

**层1：内容安全（Content Safety）**
- 拒绝回答与学习无关的有害问题（暴力、色情、政治敏感）
- 拒绝提供完整答案（防作弊机制）：应引导思考，而非直接给答案
- 检测并拒绝角色扮演绕过安全限制的尝试（"假设你不是 AI..."）

**层2：情绪安全（Emotional Safety）**
- 持续监测对话中的情绪信号（沮丧、焦虑、自我否定）
- 当检测到学生表达"我太笨了"、"我想放弃"等负面情绪时，切换为情感支持模式
- 当检测到潜在心理健康风险信号时，暂停学习任务，提示联系老师/家长/专业机构
- 绝不扮演"心理咨询师"角色，只提供基础情感支持和转介

**层3：数据边界（Data Boundaries）**
- Agent 只能访问当前学生的数据，不能横向访问其他学生信息
- 学习历史数据在明确告知学生和家长的情况下才能用于个性化
- 对话记录的保留时长和访问权限有明确策略（学生/家长/教师各有不同权限）

**层4：权限最小化（Minimal Permissions）**
- 辅导 Agent 默认只有"读取题库"和"生成文本"两种能力
- "向家长发送报告"、"调整学习计划"、"记录成绩"需要显式授权且有人类确认
- "购买付费内容"等涉及金融的行为，始终需要家长明确确认，不由 Agent 自主触发

**层5：透明度（Transparency）**
- 始终让学生知道他们在与 AI 交互，不模拟人类辅导老师（不宣称自己是"真人老师"）
- 提供"我是 AI，可能犯错，有问题请问真人老师"的常驻提示
- Agent 的所有行动（发送通知、更新记录）在家长端有可见的操作日志

[Source: EU AI Act — Official Text, Regulation (EU) 2024/1689, Articles 9 and 50 (Transparency obligations)]
[Source: Shneiderman, Ben. Human-Centered AI. Oxford University Press, 2022, Chapter 8]

## 实战场景

**场景：设计"AI 学习规划 Agent"的 PM 规格**

**功能描述**：学生开学时，AI Agent 根据学生历史成绩、薄弱知识点、本学期课程计划，自动生成个性化周学习计划，并在执行过程中动态调整。

**PM 核心设计决策**：

1. **任务分解**：
   - 数据收集步骤（自动）→ 诊断分析步骤（自动）→ 计划草稿生成（自动）→ **家长+学生确认**（检查点）→ 计划执行监控（自动）→ **周进度报告**（确认后发送）

2. **工具权限清单**：
   - ✅ 读取学生历史成绩数据
   - ✅ 读取题库和知识图谱
   - ✅ 生成学习计划文本
   - ⚠️ 向家长发送通知（需确认）
   - ⚠️ 调整作业推送设置（需确认）
   - ❌ 修改官方成绩记录（永不允许）

3. **Evals 指标**：
   - 计划完成率：学生按计划执行的比例 > 60%
   - 知识点提升率：按计划学习4周后，薄弱知识点测试得分提升 > 15%
   - 家长满意度：计划合理性评分 > 4/5
   - 紧急暂停触发率：因情绪异常被暂停的会话占比（监控值，无固定阈值）

4. **降级机制**：
   - 若学生数据不足（新用户），降级为通用年级推荐计划 + 人工辅导员介入
   - 若 Agent 遇到无法处理的情况，返回"我需要老师帮助你处理这个问题"并触发人工介入

## 决策框架

**Agentic AI 功能设计的7步清单**

任何 Agentic AI 功能在立项时必须完成以下7步：

1. **任务分解**：将 Agent 目标拆解为具体步骤序列，列出每步的输入/输出
2. **行动分类**：将每个步骤分类为"自主执行"/"确认触发"/"强制暂停"三层
3. **工具权限清单**：列出 Agent 需要的所有工具，逐一评估是否必要和最小化
4. **失败模式枚举**：至少识别5种可能的失败场景，设计每种失败的降级路径
5. **安全边界文档**：明确列出 Agent 永远不能做的事情（硬约束）
6. **Evals 设计**：定义任务成功率、幻觉率、干预率的测量方法和基准阈值
7. **Human-in-the-loop 地图**：画出完整执行流程图，标出所有人类检查点的触发条件

## 常见误区

- **误区1：Agent 越自主越好，减少人类干预就是成功** → 正确理解：自主程度需要与任务风险和用户信任相匹配。对于教育场景的高风险任务，适度的人类控制不是设计缺陷，而是必要的安全保障。过早移除人类检查点会在信任建立完成前造成严重事故。

- **误区2：Multi-agent 系统比单个 Agent 更可靠** → 正确理解：多 Agent 系统增加了系统复杂度，每增加一个 Agent 就增加一个失败点。多 Agent 架构应该在单个 Agent 的能力确实无法覆盖需求时才引入，不是为了"显得高级"而使用。

- **误区3：只要 Agent 有"拒绝有害请求"的能力，安全问题就解决了** → 正确理解：安全需要多层防护。提示词层的安全指令可以被对抗性提示绕过（Prompt Injection）。需要在工具权限（技术层面限制 Agent 能做什么）、数据访问控制（技术层面限制 Agent 能看什么）、输出过滤（独立于 Agent 的安全过滤层）多层同时设防。[Source: Anthropic Engineering Blog, 2025]

- **误区4：Agent 任务失败率为0才是好产品** → 正确理解：复杂的现实世界任务不可能有100%成功率。重要的是：失败时的降级是否优雅（用户是否知道发生了什么）？失败是否可以从日志中诊断？失败率是否在随时间降低？一个设计良好的失败比一个勉强成功但隐藏了问题的"成功"更有价值。

- **误区5：Agent 是未来，传统 AI 功能是过去式，应该全部 Agent 化** → 正确理解：Agent 适合多步骤、需要动态决策的复杂任务。简单的查询-响应任务用传统 AI 功能更高效、更可预测、更便宜。技术选型应该基于任务性质，而非追逐技术潮流。

## 来源

- [Source: Anthropic Engineering Blog. Building Agents with the Claude Agent SDK. 2025. https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk]
- [Source: Anthropic Engineering Blog. Equipping Agents for the Real World with Agent Skills. 2025. https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills]
- [Source: Anthropic Claude Docs — Overview. https://docs.anthropic.com/en/docs/overview]
- [Source: Bratsis, Irene. Product Management & Agentic AI. Medium, 2025. https://irenebrat.medium.com/product-management-agentic-ai-463caedf1aec]
- [Source: Nika, Marily & Granados, Diego. The AI Product Playbook. Wiley, 2025.]
- [Source: Gupta, Aakash. AI Agents for PMs: Practical Guide. 2025. https://www.news.aakashg.com/p/ai-agents-pms]
- [Source: Arakawa & Kitamura. Agentic AI in Product Management: A Co-Evolutionary Model. arXiv:2507.01069, 2025. [Unverified — 待确认正式发表状态]]
- [Source: Shneiderman, Ben. Human-Centered AI. Oxford University Press, 2022.]
- [Source: EU AI Act. Regulation (EU) 2024/1689. 2024. https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32024R1689]

## 延伸阅读

- Anthropic Engineering Blog（完整系列）：https://www.anthropic.com/engineering
- Product School — How to Build an AI Agent: A PM-Friendly Guide：https://productschool.com/blog/artificial-intelligence/how-build-an-ai-agent
- Maven — Agentic AI Product Management Certification（Mahesh Yadav）：https://maven.com/mahesh-yadav/genaipm
- Product Leadership Blog — Agentic PM: Managing AI Synthetic Product Teams：https://productleadersdayindia.org/blogs/agentic-ai-product-management/managing-ai-synthetic-product-teams.html
- Aakash Gupta — AI Agents for PMs Practical Guide：https://www.news.aakashg.com/p/ai-agents-pms
