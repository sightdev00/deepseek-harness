# sightdev00 DeepSeek Harness 阅读区

本目录只存放对 DeepSeek Harness 的个人源码阅读、架构分析、版本演进与实验记录，不属于上游 `deepseek-ai/deepseek-harness` 源码的一部分。

## 目录原则

- 不修改上游 `packages/`、`apps/`、`docs/`、`.agents/` 等源码与官方文档目录来承载个人笔记。
- 所有个人认知资产统一放在顶层 `sightdev00/` namespace 下，降低后续同步 upstream 时的路径冲突概率。
- 每篇源码分析必须标注分析基线版本或 commit；上游 release 后先判断哪些结论仍成立，再更新文档。
- 总览文档只维护稳定的系统模型；高频变化的实现细节、版本差异和实验记录拆分到独立文档。
- 引用外部 Harness 实践时，优先区分“源码事实”“官方经验”“社区一线问题报告”和“我的综合判断”，避免把二手经验写成源码事实。
- 不把“框架新增了什么能力”自动等同于“Agent 系统能力提升”；优先追踪真实任务、failure mode、验证闭环和 ablation 证据。

## 当前文档

1. [DeepSeek Harness 源码架构思想](01-deepseek-harness-source-architecture.md)
2. [Agent Harness 工程系统综述：DeepSeek Harness、Codex、Claude Code](02-agent-harness-engineering-systematic-review.md)
3. [从 Harness-first 到 Problem-first：Agent 系统研究的正确因果方向](03-problem-first-agent-systems-research.md)

第三篇不是简单追加 Harness 功能，而是对研究方法做上移：真实问题与可复现 failure 应先于 Runtime abstraction，生态与标准应当是长期问题求解后收敛出的结果，而不是预设目标。

## 建议的上游同步方式

```bash
git remote add upstream https://github.com/deepseek-ai/deepseek-harness.git
git fetch upstream
git checkout master
git merge upstream/master
```

如上游 master 与本 fork 的 `sightdev00/` 同时存在提交，Git 会正常做三方合并；只要上游没有创建同名个人 namespace，通常不会产生路径冲突。

## Release 更新检查

每次 DeepSeek Harness 发布新版本时，至少重新检查以下问题：

1. Cordis 的组合、依赖、effect/dispose 语义是否变化；
2. Session Event Log 与“模型可见即已记录”的不变量是否变化；
3. Agent Loop 的 Turn / Step / Inbox / Cancellation 状态机是否变化；
4. Capability Seam 的 Definition / Provider / Consumer 边界是否变化；
5. Scope、Ownership、Job、Subagent 的生命周期模型是否变化；
6. Self-modification / extensions 是否出现新的运行时修改能力；
7. 与 Codex / Claude Code 的长期运行、上下文管理、多 Agent、Worktree、测试闭环实践相比，DeepSeek Harness 是否出现新的结构性应对；
8. 新 abstraction 是否能追溯到明确的真实任务和高频 failure mode，而不是只来自通用性预期；
9. 是否开始提供 task distribution、baseline、verifier、human intervention、成本、ablation 等经验数据；
10. 社区生态是在围绕真实任务与验证闭环增长，还是主要围绕插件数量、工具数量和 UI 扩展增长；
11. 新机制属于暂时的 model-compensation layer，还是即使模型增强仍然需要的 system-invariant layer；
12. 是否出现由多个独立真实问题共同收敛出的稳定 abstraction，从而证明通用化不是提前支付的 Generalization Debt。
