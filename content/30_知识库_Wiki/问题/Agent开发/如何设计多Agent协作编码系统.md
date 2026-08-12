---
type: wiki
status: generated
created: 2026-06-26
updated: 2026-06-26
aliases:
  - 多Agent编码系统设计题
tags:
  - interview/question
  - multi-agent
  - coding-agent
confidence: medium
---

# 问题：如何设计多Agent协作编码系统？

## 30秒回答

我会避免让多个 Agent 自由聊天，而是设计清晰的角色、产物和门禁。例如 Planner 负责需求澄清和任务拆分，Generator 负责编码，Evaluator 负责评审。它们通过 spec、task、evaluation 等显式 artifact 交接，并设置最大迭代次数防止无限循环。

## 系统化回答

一个可控的多 Agent 编码系统需要：

1. Role separation：Planner / Generator / Evaluator。
2. Artifact handoff：spec.md、task.md、notes、evaluation.md。
3. Gate：用户确认 spec，Evaluator 检查 task 和 implementation。
4. Shared workspace：代码和 artifact 在文件系统共享。
5. Bounded loop：设置 max iterations。
6. Observability：每个角色的输出和决策都可追踪。

## 关键 trade-off

- 多 Agent 增强专业分工和独立评估。
- 代价是延迟、成本、状态管理复杂度上升。
- 对简单任务，单 Agent 或 workflow 更合适。

## 项目例子

- [[30_知识库_Wiki/项目/个人项目/Velaire]]
- [[30_知识库_Wiki/概念/Agent开发/多Agent编码工作流]]

## 高频追问

- 多 Agent 如何共享上下文？
- Evaluator 会不会也犯错？
- 如何防止 Planner 过度设计？
- 什么时候不该用多 Agent？
