# Harness 实验记录规范

本目录用于保存 `04-harness-deep-study-and-team-sharing-plan.md` 中的可复现实验。

实验目标不是展示功能，而是验证：

> 某个 Harness mechanism 是否真的减少了可重复 failure，并改善经过外部验证的有效工作。

---

# 1. 实验编号

```text
E01 Runtime Recovery
E02 Ownership and Concurrency
E03 Real Business Workflow
```

后续新增实验继续递增，不按产品版本重新编号。

---

# 2. 每个实验必须包含

```text
README.md
inputs/
outputs/
logs/
scripts/        # 如需要
verifier/       # 如需要
```

不强制每个目录都有内容，但 `README.md` 必须能够让另一台机器上的人理解如何复现。

---

# 3. 实验 README 模板

## Metadata

```text
Experiment ID:
Date:
DeepSeek Harness commit:
Model:
Environment:
Task:
```

## Research Question

只写一个主要问题。

## Hypothesis

明确写出实验前判断，避免看到结果以后反向组织故事。

## Baseline

说明没有目标 Harness mechanism 时如何完成同一任务。

## Variables

```text
Independent Variable:
Controlled Variables:
Observed Variables:
```

## Verifier

必须说明什么条件才算真正完成。

禁止只以：

```text
Agent says done
```

作为完成标准。

## Procedure

给出可复现步骤。

## Failure Injection

如果是 robustness / recovery 实验，明确说明如何制造故障。

## Metrics

至少选择：

```text
Verified Completion
False Completion
Human Intervention
Recovery Success
Recovery Time
Repeated Work
Wall-clock
Token / API Cost
Harness Overhead
Reproducibility
```

## Evidence

保存关键：

```text
logs
session events
commits
screenshots
verifier results
process state
```

## Result

只描述观测，不急于解释。

## Interpretation

解释机制为什么产生该结果。

## Alternative Explanation

至少给出一个竞争性解释。

## Ablation

如果增加了某个 Harness mechanism，尽可能移除它重新运行。

## Conclusion

结论必须限制在本实验能够支持的范围内。

## Open Questions

记录下一步，而不是用总结掩盖未解决问题。

---

# 4. 证据标记

实验报告统一使用：

```text
SOURCE
OFFICIAL
EXPERIMENT
JUDGMENT
```

其中本目录原则上以 `EXPERIMENT` 为核心。

---

# 5. 实验纪律

1. 先写 hypothesis，再运行；
2. baseline 与实验组必须完成同一任务；
3. 尽量一次只改变一个主要机制；
4. 模型版本、Harness commit、环境必须记录；
5. 成功案例和失败案例都保留；
6. 不因为一次成功就声称机制普遍有效；
7. 不因为一次失败就声称某框架普遍无效；
8. 优先保留原始日志和 verifier evidence；
9. 真实业务任务优先于人为 demo；
10. 最终判断必须允许未来版本反证。
