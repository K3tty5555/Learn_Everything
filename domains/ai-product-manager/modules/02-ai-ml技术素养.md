---
module: "02"
title: AI/ML 技术素养
domain: ai-product-manager
sources:
  - Huyen-DMLS-2022
  - Bratsis-Handbook-2023
  - Lipenkova-Art-2024
last_updated: 2026-03-31
---

# 模块02：AI/ML 技术素养

## 为什么必须懂这个

PM 不需要会写模型训练代码，但必须能与数据科学家和 ML 工程师在同一个认知框架内对话。不懂技术的 PM 在 AI 团队中面临两种危险：一是被工程师"教育"——工程师用技术复杂性为你设定不合理的边界；二是做出脱离技术可行性的产品承诺，导致团队陷入"承诺了做不到的功能"的困境。

对于教育 AI PM 而言，技术素养还有一层特殊意义：你需要能评估供应商（如科大讯飞、商汤等）的技术方案是否真实可靠，而不是被 PPT 中亮眼的准确率数字所迷惑。

## 核心内容

### 机器学习基础：PM 需要的最小知识单元

**什么是机器学习（Machine Learning）**

机器学习是从数据中自动学习规律、构建预测模型的方法集合，区别于传统软件的"显式规则编程"。传统代码是 `IF 分数 < 60 THEN 推送补题`，ML 模型是"从10万条学习记录中学习哪些特征组合预测了未来低分"。

PM 需要理解的三种核心学习范式：
- **监督学习（Supervised Learning）**：有标注数据，学习输入到输出的映射。教育场景：作文评分、知识点掌握程度判断。
- **无监督学习（Unsupervised Learning）**：无标注数据，发现数据内在结构。教育场景：学习行为聚类、发现学生群体画像。
- **强化学习（Reinforcement Learning）**：通过奖惩信号学习最优策略。教育场景：自适应学习路径优化（智能推题顺序）。

[Source: Huyen, Chip. Designing Machine Learning Systems. O'Reilly, 2022, Chapter 1]

**模型、训练与推理的区别**

- **模型（Model）**：从数据中学到的参数化函数，可以理解为"学习成果的结晶"。
- **训练（Training）**：用历史数据调整模型参数的过程，通常耗时且成本高昂。
- **推理（Inference）**：用训练好的模型对新输入产生预测输出的过程，是用户每次使用功能时实际发生的事。

PM 关注点：训练是一次性/周期性成本，推理是持续性边际成本。产品的规模化成本主要来自推理。[Source: Huyen, Chip. Designing Machine Learning Systems. O'Reilly, 2022, Chapter 7]

**关键性能指标：PM 必须能读懂**

| 指标 | 含义 | PM 关注点 |
|------|------|----------|
| 准确率（Accuracy） | 整体预测正确的比例 | 适用于类别均衡场景 |
| 精确率（Precision） | 预测为正的样本中真正为正的比例 | 关注误报率时用（错误推送补题很烦） |
| 召回率（Recall） | 真正为正的样本中被预测到的比例 | 关注漏报率时用（漏掉需要干预的学生） |
| F1 分数 | 精确率和召回率的调和平均 | 两者都重要时的综合指标 |
| AUC-ROC | 模型区分能力的综合度量 | 评估分类模型整体质量 |

**重要：准确率95%意味着什么？** 在一个1%为正例的不平衡数据集上（如识别学习障碍学生），全部预测为负例也能达到99%准确率。这是为什么 PM 不能只看准确率的原因。[Source: Huyen, Chip. Designing Machine Learning Systems. O'Reilly, 2022, Chapter 6]

### LLM 基础：大语言模型的 PM 认知框架

**大语言模型（Large Language Model, LLM）** 是通过在海量文本上预训练的神经网络，能理解和生成自然语言。代表性模型包括 GPT-4o（OpenAI）、Claude 3.7（Anthropic）、Gemini 2.0（Google）。

**PM 必须理解的 LLM 特性**：

1. **概率性输出（Probabilistic Output）**：LLM 不是数据库查询，每次相同输入可能产生不同输出。这意味着传统 QA 测试（写一个 test case，期望固定输出）对 LLM 功能不适用。你需要 Evals（评估体系），而不是单元测试。

2. **上下文窗口（Context Window）**：模型在一次对话中能"记住"的文本量上限。GPT-4 Turbo 约 128K tokens，Claude 3.7 约 200K tokens。超出窗口的内容会被截断或遗忘。PM 影响：长文档处理、多轮对话、学生学习历史的检索策略都受此约束。

3. **幻觉（Hallucination）**：模型生成自信但不准确的内容。在教育场景中，这是红线风险——错误的数学解题步骤、不存在的历史事件，都可能对学生产生严重误导。

4. **Temperature（温度）参数**：控制输出随机性。Temperature=0 时输出确定性最高（适合判断题解析）；Temperature=1 时输出更多样化（适合创意写作辅导）。PM 在写功能规格时需要明确指定合适的 temperature 范围。

5. **Few-shot Learning（少样本学习）**：通过在提示词中给出少量示例，引导模型输出符合预期格式的内容。这是 PM 控制 LLM 行为的重要工具，无需微调。

[Source: Nika, Marily. Building AI-Powered Products. O'Reilly, 2025, Chapter 4]

### PM 需要懂多少技术：边界划定

**必须掌握（自己能做判断）**：
- 能区分分类/回归/生成任务，知道各自适用场景
- 能理解数据质量如何影响模型质量（Garbage In, Garbage Out）
- 能读懂基本的模型评估报告，识别过拟合（Overfitting）迹象
- 能理解 API 调用延迟（Latency）、吞吐量（Throughput）对用户体验的影响
- 能判断一个功能是否需要微调（Fine-tuning）还是能用提示词工程（Prompt Engineering）解决

**必须能问清楚（让工程师解释给你听）**：
- 训练这个模型需要多少标注数据？数据从哪里来？
- 模型在什么条件下会失效？边缘情况（Edge Cases）是什么？
- 模型的推理延迟是多少毫秒？能否支撑实时交互？
- 这个模型有没有经过公平性（Fairness）测试？在不同年级、不同地区学生上的表现是否一致？

**不需要掌握**：
- 具体的神经网络架构设计（Transformer 内部结构）
- 超参数调优细节
- 分布式训练系统配置

[Source: Bratsis, Irene. The AI Product Manager's Handbook. Packt, 2023, Chapter 2]

### 与工程师的对话框架

**"五问法"：评估任何 AI 功能提案时的核对清单**

1. **数据问题**："训练/使用这个模型需要什么数据？我们现有数据的覆盖率和质量如何？"
2. **性能边界**："这个模型在什么情况下表现好？在什么情况下会失败？准确率是在什么数据分布上测量的？"
3. **延迟与成本**："实时推理的延迟是多少？每次推理的成本是多少？规模化后的成本曲线是什么？"
4. **监控与维护**："上线后如何监控模型性能？什么情况下需要重新训练？"
5. **可解释性**："模型给出结论时，用户能知道为什么吗？对于错误结论，我们能诊断原因吗？"

**如何防止被技术复杂性绑架**：当工程师说"这个功能技术上做不到"时，作为 PM 你需要追问："做不到"的具体约束是什么——是数据不足？算法能力边界？还是时间和成本限制？这三类约束的解决路径完全不同。

[Source: Lipenkova, Janna. The Art of AI Product Development. Manning, 2024, Chapter 3]

## 实战场景

**场景：评估供应商的 AI 作文批改方案**

智学网正在评估一家供应商提供的 AI 作文批改系统，对方宣称"准确率达到人工评分的95%一致率"。

**懂技术的 PM 的提问清单**：
1. 95%一致率是在什么测试集上计算的？测试集是否覆盖了高中、初中不同年级和作文类型？
2. "一致"的定义是什么——总分一致，还是维度分（内容/语言/结构）都一致？
3. 在长文章（800字以上）和短文章上，性能是否有差异？
4. 系统是否处理过包含错别字、非标准用语的真实学生作文？
5. 评分延迟是多少秒？能否支撑课堂实时批改场景？

这些问题的答案会直接决定这个供应商方案是否真的符合智学网的使用场景。

## 决策框架

**AI 功能技术评估 3×3 矩阵**

在评估任何 AI 功能的技术方案时，从三个维度 × 三个层次进行评估：

| 维度 | 当前状态 | 6个月后 | 规模化后 |
|------|---------|---------|---------|
| 准确性 | 测试集表现 | 真实用户数据表现 | 数据分布变化后表现 |
| 成本 | 原型阶段成本 | MVP 规模成本 | 全量用户成本 |
| 运维 | 初始部署复杂度 | 日常监控负担 | 模型更新迭代频率 |

任何一格出现严重红灯，都需要在立项前制定明确的应对策略。

## 常见误区

- **误区1：更大的模型 = 更好的产品** → 正确理解：更大的模型意味着更高的推理成本和延迟。在教育场景的大规模并发下，一个轻量级但调优良好的专用模型，往往优于一个通用大模型。Fit-for-purpose 比 State-of-the-art 更重要。

- **误区2：模型准确率达标就可以上线** → 正确理解：准确率只是上线条件之一。还需要验证：在真实用户数据（非测试集）上的表现、用户对错误输出的容忍度、错误时的降级（Fallback）机制是否就绪。[Source: Huyen, Chip. Designing Machine Learning Systems. O'Reilly, 2022, Chapter 9]

- **误区3：技术上可行就代表产品上应该做** → 正确理解：技术可行性是必要条件，不是充分条件。问题是否值得解决（用户痛点）、是否有更简单的非 AI 解法、AI 解法是否带来了额外的信任和伦理风险，才是 PM 真正需要评估的核心问题。

- **误区4：LLM 会持续进步，现在不好用以后就好了** → 正确理解：虽然 LLM 能力确实在进步，但你的具体产品问题不会自动被解决。你需要评估当前能力是否满足用户需求的最低门槛，而不是赌未来的技术进步。[Source: Rathje et al., 2025, Management Review Quarterly]

## 来源

- [Source: Huyen, Chip. Designing Machine Learning Systems. O'Reilly, 2022.]
- [Source: Bratsis, Irene. The AI Product Manager's Handbook. Packt, 2023.]
- [Source: Lipenkova, Janna. The Art of AI Product Development. Manning, 2024.]
- [Source: Nika, Marily. Building AI-Powered Products. O'Reilly, 2025.]
- [Source: Rathje et al. Where does AI play a major role in NPD? Management Review Quarterly, 2025.]

## 延伸阅读

- Chip Huyen《Designing Machine Learning Systems》第1、6、7、9章：ML 系统的全生命周期
- Google Machine Learning Crash Course（免费）: https://developers.google.com/machine-learning/crash-course
- Andrej Karpathy — Neural Networks: Zero to Hero（YouTube 系列）：直观理解神经网络
- 3Blue1Brown — Neural Networks 系列（YouTube）：视觉化理解深度学习
