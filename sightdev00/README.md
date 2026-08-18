# sightdev00 DeepSeek Harness 阅读区

本目录只存放对 DeepSeek Harness 的个人源码阅读、架构分析、版本演进与实验记录，不属于上游 `deepseek-ai/deepseek-harness` 源码的一部分。

## 目录原则

- 不修改上游 `packages/`、`apps/`、`docs/`、`.agents/` 等源码与官方文档目录来承载个人笔记。
- 所有个人认知资产统一放在顶层 `sightdev00/` namespace 下，降低后续同步 upstream 时的路径冲突概率。
- 每篇源码分析必须标注分析基线版本或 commit；上游 release 后先判断哪些结论仍成立，再更新文档。
- 总览文档只维护稳定的系统模型；高频变化的实现细节、版本差异和实验记录拆分到独立文档。
- 引用外部 Harness 实践时，优先区分“源码事实”“官方经验”“社区一线问题报告”和“我的综合判断”，避免把二手经验写成源码事实。

## 当前文档

1. [DeepSeek Harness 源码架构思想](01-deepseek-harness-source-architecture.md)
2. [Agent Harness 工程系统综述：DeepSeek Harness、Codex、Claude Code](02-agent-harness-engineering-systematic-review.md)

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
7. 与 Codex / Claude Code 的长期运行、上下文管理、多 Agent、Worktree、测试闭环实践相比，DeepSeek Harness 是否出现新的结构性应对。
