# sightdev00 阅读笔记

本目录是 `sightdev00/deepseek-harness` fork 的个人源码阅读与架构研究区，与 DeepSeek Harness 上游源码隔离维护。

## 目录约束

- 所有个人分析、源码地图、版本演进记录只放在 `sightdev00/` 下。
- 不因“写笔记”修改 `packages/`、`apps/`、`docs/`、`.agents/` 等上游目录。
- 文档引用上游实现时使用相对路径，并记录分析基线 commit/tag。
- 上游源码变化后，先同步源码，再更新本目录中的分析；不要反向把个人结论写进上游源码文件。

这样做的目的，是让 fork 中的“上游源码”和“个人认知资产”在 Git 路径层面分离。只要上游不创建同名 `sightdev00/` 路径，常规 upstream merge 不会与本目录产生内容冲突。

## 当前文档

1. [DeepSeek Harness 源码架构思想](01-deepseek-harness-source-architecture.md)

## 推荐的上游同步方式

首次配置：

```bash
git remote add upstream https://github.com/deepseek-ai/deepseek-harness.git
git fetch upstream
```

后续同步：

```bash
git checkout master
git fetch upstream
git merge upstream/master
git push origin master
```

由于 fork 自己包含 `sightdev00/` 下的提交，`master` 不再与 upstream 保持纯 fast-forward 关系；这里优先采用普通 merge，而不是强制覆盖 fork。只要个人修改严格留在本目录，上游更新通常只会形成无冲突合并。

## 文档更新原则

每次 DeepSeek Harness 发布新 RC / Release 时，至少检查：

1. `docs/architecture.zh.md`：官方架构契约是否变化；
2. `docs/module-graph.zh.md`：模块组和依赖方向是否变化；
3. `packages/core/agent-loop`：主控制流是否变化；
4. `packages/core/session`：持久事实与重放规则是否变化；
5. `packages/core/tools`：工具执行流水线是否变化；
6. `packages/llm`：Adapter seam 与模型能力是否变化；
7. `packages/*` 新增模块：是否出现新的通用 capability seam；
8. `.agents/notes/implemented/`：新功能背后的设计决策、否决方案与 trade-off。

重点维护“系统模型”和“设计原则”，而不是逐版本堆积改动列表。
