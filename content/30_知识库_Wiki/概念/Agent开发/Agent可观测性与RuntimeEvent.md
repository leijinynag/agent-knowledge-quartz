---
type: wiki
status: generated
created: 2026-06-26
updated: 2026-06-26
aliases:
  - Agent Observability RuntimeEvent
  - Agent事件日志
tags:
  - agent-development
  - observability
  - runtime-event
confidence: medium
---

# Agent可观测性与RuntimeEvent

## 一句话定义

Agent 可观测性是把模型输出、工具调用、审批决策、错误、成本和状态变化记录成可追踪事件，帮助调试、回放和评估 Agent 行为。

## 核心要点

- Agent 的问题常常出在过程，而不只是最终答案。
- 需要记录：模型请求、文本增量、工具调用、工具结果、审批、错误、token/usage、完成状态。
- 事件日志可以支持 replay、debug、Workbench timeline、metrics 和回归测试。

## 在 Velaire 中的体现

Velaire 通过 RuntimeEvent 统一 Runtime 输出，Workbench 可以展示 conversation、timeline、inspector、metrics、diff 和 session run log。

相关项目：[[30_知识库_Wiki/项目/个人项目/Velaire]]

## 面试表达

> Agent 系统要可观测，否则失败时不知道是模型没理解、工具失败、权限被拒、上下文缺失还是 provider 中断。我把 Runtime 的关键步骤都事件化，方便 UI 展示、日志记录和测试断言。

## 高频追问

- 哪些事件必须记录？
- 如何做 Agent replay？
- 如何统计工具成功率和失败率？
- 如何排查 Agent 没完成任务的原因？

## 相关页面

- [[30_知识库_Wiki/概念/Agent开发/事件驱动Agent Runtime]]
- [[30_知识库_Wiki/主题/Agent开发/Agent评估与可观测性]]
