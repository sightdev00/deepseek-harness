# Harness 深度研究与小组分享计划

> 计划周期：2026-08-19 ～ 2026-09-27，共 6 周。
>
> 研究对象：以 DeepSeek Harness 为主轴，同时用 OpenAI Codex 与 Anthropic Claude 的 Harness / Agent Engineering 实践做对照。
>
> 最终目标：不是“读完 DeepSeek Harness”，而是建立一套能够解释、验证、批判和迁移 Harness 设计的系统认知，并完成一次面向研发小组的高质量技术分享。

---

# 0. 最终验收标准

这轮研究完成时，至少要达到四个层次。

## L1：能讲清源码

不依赖源码搜索，能够从一张空白纸开始画出 DeepSeek Harness 的核心运行链路，并解释：

```text
Profile / Bundle / Patch
        ↓
Cordis Plugin Tree
        ↓
Agent / Agent Loop
        ↓
Session Event Log
        ↓
LLM Runtime
        ↓
Tool Pipeline
        ↓
Capability Provider
        ↓
Execution Environment
```

同时能回答：

- Cordis 为什么存在，而不是普通 DI / EventBus；
- `effect / dispose` 在 Agent Runtime 中解决什么现实问题；
- Session Event Log 保存什么，不保存什么；
- `Model-visible means recorded` 为什么是重要不变量；
- Agent Loop 的 Turn / Step / Inbox / Cancellation 怎么推进；
- Tool Definition / Provider / Consumer 为什么要分层；
- Scope / Ownership / Job / Subagent 如何约束资源生命周期；
- Runtime composability 与普通 plugin framework 的区别在哪里。

## L2：能比较不同 Harness 哲学

不做产品功能罗列，而是能解释三条路线各自在解决什么问题：

```text
DeepSeek
Runtime Composability

OpenAI Codex
Agent-readable Repository / Environment

Anthropic Claude
Long-running Protocol / Evaluation / Multi-agent
```

并能指出哪些概念实际上同构，哪些只是表面相似。

## L3：能用实验判断机制价值

至少完成 3 类实验：

1. **Runtime / Recovery 实验**：长任务中断、恢复、上下文变化后，系统能否继续；
2. **Ownership / Multi-agent 实验**：并发 Agent、Job、Process、Workspace 的资源归属与清理；
3. **真实业务实验**：把 Harness 用到一个已经真实存在、可以独立验收的研发任务，而不是为 Harness 人工制造 demo。

每个实验必须有 baseline、verifier、failure injection 和可复现记录。

## L4：能形成自己的判断

最终分享不能停留在：

> DeepSeek Harness 有哪些模块。

必须能够回答：

> **什么时候 Harness 真正创造价值？什么时候只是复杂框架？哪些机制是模型补偿层，哪些是长期系统不变量？为什么 Harness 的正确研究路径应该从真实问题和 failure mode 出发？**

---

# 1. 最终产物

六周后至少形成以下研究资产。

```text
sightdev00/
├── 01-deepseek-harness-source-architecture.md
├── 02-agent-harness-engineering-systematic-review.md
├── 03-problem-first-agent-systems-research.md
├── 04-harness-deep-study-and-team-sharing-plan.md
├── 05-harness-runtime-deep-dive.md
├── 06-harness-failure-taxonomy-and-evidence.md
├── 07-harness-experiment-report.md
├── 08-harness-team-sharing-outline.md
└── experiments/
    ├── README.md
    ├── E01-runtime-recovery/
    ├── E02-ownership-and-concurrency/
    └── E03-real-business-workflow/
```

`05` ～ `08` 在研究推进过程中创建，不预先填空文档。

最终小组分享建议控制在：

```text
主讲 50～60 min
Demo 10～15 min
讨论 15～20 min
```

目标不是一次“大而全介绍”，而是让听众形成一个稳定判断：

> **Agent = Model + Harness 只是起点；真正决定 Agent 能否进入生产环境的是任务、环境、状态、反馈、验证、权限、恢复和组织方式共同形成的系统。**

---

# 2. 研究方法：每个问题都走完整闭环

禁止采用：

```text
读文档
↓
记功能
↓
写总结
```

统一采用：

```text
提出问题
   ↓
读官方设计 / 源码
   ↓
沿真实调用链 trace
   ↓
建立最小实验
   ↓
主动制造 failure
   ↓
记录系统行为
   ↓
与 Codex / Claude 对照
   ↓
形成判断
   ↓
寻找反例
   ↓
决定结论是否成立
```

任何重要结论都至少标记为以下四种证据之一：

- `SOURCE`：DeepSeek Harness 当前源码事实；
- `OFFICIAL`：DeepSeek / OpenAI / Anthropic 官方公开经验；
- `EXPERIMENT`：自己可复现的实验结果；
- `JUDGMENT`：在前三类证据基础上的综合判断。

不能用 `JUDGMENT` 冒充 `SOURCE`。

---

# 3. 十二个核心研究问题

这 12 个问题是整轮学习的索引，不按 package 被动阅读。

## R1. Harness 到底是什么

需要区分：

```text
LLM wrapper
Agent framework
Agent runtime
Workflow engine
Tool registry
Orchestrator
Harness
Agent system
```

最后给出自己的 operational definition，而不是重复厂商定义。

## R2. Composition：为什么要可组合

研究：

- Cordis Context / inject；
- Plugin Tree；
- Profile / Bundle / Patch；
- Dependency；
- Effect / Dispose；
- Typed Events / Waterfall。

核心问题：

> 哪些变化轴真的需要 abstraction seam，哪些只是框架为了通用性预先制造的扩展点？

## R3. Durable State：系统绝不能忘什么

研究：

- Session Event Log；
- Projection / Replay；
- Persistence；
- Compaction；
- request/header、assistant/message、tool/result 等 durable facts。

核心问题：

> Conversation、working context、durable task state、repository knowledge 应如何分层？

## R4. Agent Loop：模型调用之外还发生了什么

逐步 trace：

```text
Inbox
→ pre-step
→ derive messages
→ context assembly
→ LLM request
→ streaming
→ tool call
→ tool result
→ next step
→ finish / cancellation
```

要求最终能解释每个阶段是谁推进状态、谁持久化事实、谁有权终止。

## R5. Capability：Tool 为什么不能只是函数表

研究：

- Tool schema；
- Provider / Consumer seam；
- FS / Shell / Web / MCP；
- Policy / Permission；
- Sandbox。

核心问题：

> “模型能调用工具”和“系统提供可靠能力边界”之间差了什么？

## R6. Ownership：长期 Agent 最容易被低估的问题

研究：

```text
Agent
Job
Subagent
Process
AbortSignal
Workspace
Session
Attachment
```

每一种资源都必须回答：

```text
谁创建？
谁拥有？
谁等待？
谁取消？
谁清理？
失败时残留什么？
```

## R7. Context / Memory / Knowledge

区分：

```text
Prompt context
Conversation history
Compaction summary
Session durable facts
Project documentation
Learned memory
Task checkpoint
```

重点验证：

> Compaction 为什么不能等价于 memory；Conversation 为什么不能成为长期系统记录。

## R8. Long-running：跨 Context Window 如何继续

研究：

- checkpoint；
- task / plan；
- Git state；
- progress artifact；
- recovery；
- Anthropic initializer / feature list / planner / generator / evaluator；
- DeepSeek 当前 Runtime 在 macro loop 上提供了什么、缺什么。

## R9. Multi-agent：真正的问题是不是 Spawn

研究：

```text
Context isolation
Filesystem isolation
Git / worktree
Permission inheritance
Process ownership
Result aggregation
Cancellation
Cost amplification
Conflict
```

核心判断：

> Multi-agent 的第一问题通常不是“如何并行”，而是 ownership、isolation、coordination 和 verification。

## R10. Verification：谁有权宣布任务完成

建立完成状态的层级：

```text
Model self-report
< Static checks
< Unit test
< Integration test
< E2E / environment verification
< Independent evaluator
< Human / business acceptance
```

重点研究 false completion。

## R11. Economics：Harness 是否真的提高生产率

不能只看 agent benchmark。

至少记录：

```text
verified completion rate
human interventions
false completion rate
recovery time
wall-clock time
token / API cost
parallel amplification
setup / maintenance cost
reproducibility
```

最终指标优先考虑：

> **verified useful work / human attention**

## R12. Generalization：什么时候才有资格抽象

对每个通用机制追问：

```text
它来自几个独立真实问题？
它解决的是高频 failure 还是想象中的未来需求？
移除后真实任务是否显著变差？
模型升级以后还需要吗？
```

目标是区分：

```text
Model-compensation layer
vs
System-invariant layer
```

并持续检查 Generalization Debt。

---

# 4. 六周执行计划

## Week 1：源码主干彻底打通

时间：2026-08-19 ～ 2026-08-23

### 目标

从“知道模块”进入“能沿执行链解释因果”。

### 工作

1. 固定当前 DeepSeek Harness commit / release baseline；
2. 重新走一遍 Cordis 最小示例；
3. trace 一个最小 user message 从输入到 LLM、tool、result、session 的完整路径；
4. trace cancellation / dispose；
5. trace一个 subagent / job 生命周期；
6. 为每个核心对象记录：
   - 输入；
   - 输出；
   - 状态；
   - owner；
   - lifecycle；
   - failure path。

### 必须产出

开始形成：

`05-harness-runtime-deep-dive.md`

其中至少包含 4 张自己重新画的图：

```text
A. Composition Graph
B. One Turn Data Flow
C. Ownership / Lifecycle Graph
D. Durable State Graph
```

### 周验收

关闭源码后，15 分钟内白板画出核心 Runtime；再打开源码逐项核查，重大错误不超过 3 处。

---

## Week 2：从 happy path 转向 failure path

时间：2026-08-24 ～ 2026-08-30

### 目标

不再只研究“怎么跑”，开始研究“怎么坏”。

### 工作

主动追踪 / 制造以下 failure：

```text
tool failure
provider unavailable
process cancellation
session interruption
invalid state
subagent failure
long output / retention
permission denial
partial execution
restart / resume
```

同时阅读 DeepSeek `.agents/notes` 中与这些机制有关的设计决策。

### 必须产出

建立：

`06-harness-failure-taxonomy-and-evidence.md`

Failure 统一记录成：

```text
Failure ID
Trigger
Observable Symptom
Root Cause
Current Guardrail
Recovery Path
Durable Evidence
Human Intervention
Remaining Risk
```

### 周验收

至少记录 10 个 failure case，其中至少 5 个来自自己可复现实验，而不是 issue / 文档转述。

---

## Week 3：Codex / Claude 对照研究

时间：2026-08-31 ～ 2026-09-06

### 目标

回答：

> DeepSeek Harness 的设计到底是普遍规律、特定实现，还是过早抽象？

### 对照维度

只按共同问题比较，不按产品 feature 比较：

| 问题 | DeepSeek | Codex | Claude |
|---|---|---|---|
| Durable state | | | |
| Context recovery | | | |
| Repository knowledge | | | |
| Tool / capability | | | |
| Permission / policy | | | |
| Ownership | | | |
| Worktree / isolation | | | |
| Subagent | | | |
| Long-running protocol | | | |
| Verification | | | |
| Observability | | | |
| Harness evolution | | | |

### 特别任务

对每个 DeepSeek 核心 abstraction 做一次反事实：

> 如果删除它，用更简单结构实现，真实任务会失去什么？

### 周验收

至少形成 5 个“看起来类似但实际上不同”的案例，以及 5 个“三家通过不同实现共同收敛”的系统不变量。

---

## Week 4：建立受控 Harness 实验

时间：2026-09-07 ～ 2026-09-13

### 目标

第一次把判断从源码阅读变成数据。

### E01：Runtime Recovery

选一个需要多轮工具调用、会修改多个文件并运行测试的任务。

至少比较：

```text
A. 简单 Agent / 最少 Harness baseline
B. DeepSeek Harness 正常路径
C. 中途人为中断后恢复
D. 故意丢失部分 working context 后恢复
```

记录：

- 是否找到正确执行位置；
- 是否重复工作；
- 是否忘记约束；
- 是否产生 false completion；
- 恢复耗时；
- 人工干预次数。

### E02：Ownership and Concurrency

构造：

```text
parent agent
├── subagent A
├── subagent B
└── background job
```

主动测试：

- parent cancellation；
- child failure；
- shared file / workspace；
- process residue；
- permission boundary；
- dispose / cleanup。

### 周验收

所有结论必须可以从实验目录一条命令或明确步骤重新复现。

---

## Week 5：真实业务问题实验

时间：2026-09-14 ～ 2026-09-20

### 目标

这是整轮研究最重要的一周。

不是继续给 Harness 找 demo，而是让 Harness 进入一个已经存在的工作流。

### 首选任务

优先选择已有 AgentWorkbench 中具有明确输入、输出、CLI 和验收条件的任务，例如：

```text
ONNX detection validation
或
Dataset analysis
```

它们适合作为研究对象，是因为：

```text
真实反复发生
输入输出明确
有可独立运行的非 Agent 实现
可以自动验证
包含需要 Agent 判断的分支
失败能够定位
```

### 实验设计

至少比较：

```text
Baseline：人工 + CLI
A：模型直接调用工具
B：DeepSeek Harness 单 Agent
C：增加 durable task state / verifier
D：必要时增加 subagent
```

注意：不能默认 D 最强。

每增加一个 Harness 组件，都要做 ablation：

```text
加它以后哪个 failure 显著减少？
移除它以后哪个指标显著退化？
```

### 周验收

回答三个问题：

1. 哪些 Harness 能力确实减少了人工注意力；
2. 哪些能力没有收益，甚至增加复杂度；
3. 哪些业务知识必须存在于 domain workflow，而不应该进入通用 Harness core。

---

## Week 6：综合、反证与小组分享

时间：2026-09-21 ～ 2026-09-27

### 目标

把“我研究了什么”转化为“团队以后应该如何判断和建设 Agent 系统”。

### 工作 1：完成实验报告

形成：

`07-harness-experiment-report.md`

只保留：

```text
问题
baseline
实验
证据
结论
反例
未解决问题
```

避免流水账。

### 工作 2：建立最终系统模型

要求用一张图同时容纳：

```text
Business Problem
Task State
Model
Harness
Capabilities
Environment
Verification
Human
Failure / Recovery
```

并明确：Harness 只是系统中的一层。

### 工作 3：设计小组分享

形成：

`08-harness-team-sharing-outline.md`

建议结构：

```text
Part 1  5 min
为什么最近大家都在谈 Harness

Part 2  10 min
Harness 到底是什么，不是什么

Part 3  15 min
拆 DeepSeek Harness：Runtime / State / Capability / Ownership

Part 4  10 min
Codex / Claude 为什么走出了不同路线

Part 5  10 min
三个真实实验：哪里成功，哪里失败

Part 6  10 min
核心判断：Problem-first，而不是 Framework-first

Demo   10～15 min
展示一个 failure → recovery → verifier 的真实闭环
```

### 工作 4：反方审查

分享前专门站到 DeepSeek Harness 支持者立场，攻击自己的观点：

```text
为什么 ecosystem-first 可能是对的？
为什么 plugin architecture 可能正是快速寻找问题的工具？
为什么统一 Runtime 能降低企业采用成本？
为什么现在看不到 business evidence 只是项目太早？
```

只有经得住这一轮，最终判断才进入分享。

### 最终验收

不开文档，能够连续讲 45 分钟，并回答至少以下问题：

1. Harness 与 Agent Framework 有什么本质差别？
2. 为什么 Agent Loop 不是 Harness 的全部？
3. 为什么 durable state 比 memory 更基础？
4. 为什么 multi-agent 首先是 ownership 问题？
5. 为什么 worktree 是 execution realm，而不只是 Git 技巧？
6. 谁应该有权宣布任务完成？
7. 为什么 Harness 组件需要 ablation？
8. DeepSeek / OpenAI / Anthropic 各自最强的系统思想是什么？
9. DeepSeek Harness 当前最可能的 abstraction risk 在哪里？
10. 一个真正值得 Agent 化的业务任务应该满足什么条件？
11. 如何衡量 Harness 是否真的提高生产率？
12. 为什么生态应该是问题求解后的结果，而不应只是预设目标？

---

# 5. 三个实验的共同指标

统一记录，避免不同实验无法比较。

| 指标 | 含义 |
|---|---|
| Verified Completion | 通过外部 verifier 的任务完成率 |
| False Completion | Agent 宣布完成但 verifier 不通过 |
| Human Intervention | 人需要纠偏 / 补信息 / 重启的次数 |
| Recovery Success | 中断后能否正确恢复 |
| Recovery Time | 从失败到重新进入正确执行路径的时间 |
| Repeated Work | 恢复后重复探索 / 重复修改的程度 |
| Wall-clock | 完成任务总时间 |
| Token / API Cost | 模型调用成本 |
| Harness Overhead | 为运行 Harness 额外付出的配置、维护和执行成本 |
| Reproducibility | 同任务重复运行能否稳定得到可接受结果 |

最终不要只追求某个单指标最优，而要看：

> **单位人类注意力能够获得多少经过验证的有效工作。**

---

# 6. 每天的最小研究节奏

在正常工作期间，不要求每天大块时间。

建议：

```text
工作日：60～90 min
周末：一次 3～4 h 深度实验
```

每次研究结束前必须写三行：

```text
今天确认了什么？
今天推翻了什么？
下一步最值得验证的问题是什么？
```

如果当天只是“读了很多”，但这三行写不出来，则当天研究质量不合格。

---

# 7. 阅读优先级

## P0：源码与仓库事实

优先级最高：

```text
packages/core/*
docs/architecture*
docs/cordis*
docs/tool*
.agents/notes
相关 tests
```

源码和 test 优先于 README 宣传描述。

## P1：官方一线工程经验

重点不是收集新闻，而是追 load-bearing evidence：

```text
DeepSeek Harness architecture / design notes
OpenAI Harness Engineering / Codex agent-first engineering
Anthropic long-running harness / multi-agent / evaluator experiments
```

## P2：真实 failure reports

GitHub Issues / Discussions 只作为 failure sample，不直接作为普遍结论。

## P3：第三方框架

只有在出现明确对照问题时才扩展到：

```text
LangGraph
AutoGen
OpenCode
CrewAI
其他 runtime / orchestration framework
```

避免研究范围无限扩张。

---

# 8. 明确不做什么

为了“研究透”，更要防止范围膨胀。

本轮不追求：

- 把所有 Agent Framework 都看一遍；
- 枚举所有 DeepSeek Harness plugin；
- 做插件数量统计；
- 做模型能力 benchmark；
- 为了演示而开发没有真实使用价值的 Agent；
- 先设计自己的通用 Harness；
- 用单次成功 demo 证明架构正确；
- 把 Claude / Codex 的产品差异当成架构结论。

---

# 9. 研究中止 / 转向条件

如果出现以下证据，要主动修改研究假设：

## 条件 A

DeepSeek 后续迅速给出大量真实业务 benchmark、ablation 和长期运行数据。

则重新评估“ecosystem-first”的判断，不能固守当前观点。

## 条件 B

实验发现复杂 Harness 与最小 Agent baseline 相比没有稳定收益。

则重点研究：

> 哪些 Harness mechanism 已被强模型能力吞掉。

## 条件 C

真实业务实验发现最大瓶颈根本不是 Runtime，而是 verifier / data / domain knowledge。

则研究重点向 Agent System 上层迁移，不为了保持 Harness 主题而强行留在 Runtime。

## 条件 D

多个不同业务问题独立地产生同一种机制需求。

这时才把它提升为候选通用 abstraction。

---

# 10. 这轮研究真正要留下什么

六周后真正有价值的资产，不应该是：

```text
我看过 DeepSeek Harness
```

而应该是：

```text
一套源码系统模型
+
一套 failure taxonomy
+
一套可重复实验方法
+
一批真实 failure evidence
+
一套判断 abstraction 是否成立的方法
+
一个业务 Agent 系统案例
+
一次可以让团队形成共同语言的分享
```

最后能够形成下面这个稳定研究循环：

```text
Real Problem
    ↓
Observe Failure
    ↓
Model Failure Mechanism
    ↓
Minimal Harness Intervention
    ↓
Verify / Ablate
    ↓
Accumulate Failure Knowledge
    ↓
Cross-domain Repetition
    ↓
Stable Abstraction
```

这比“熟悉一个 Harness 框架”更接近真正的 Agent Systems Engineering。
