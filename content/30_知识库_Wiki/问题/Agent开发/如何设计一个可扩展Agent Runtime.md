---
type: wiki
status: generated
created: 2026-06-26
updated: 2026-06-26
aliases:
  - 可扩展Agent Runtime设计题
tags:
  - interview/question
  - agent-development
confidence: medium
---

# 问题：如何设计一个可扩展Agent Runtime？

## 30秒回答

我会把 Agent Runtime 拆成模型适配层、运行时状态机、工具执行层、策略权限层和事件输出层。Runtime 不直接绑定某个模型或 UI，而是消费统一的模型事件，执行工具调用，并通过 RuntimeEvent 输出过程，这样 CLI、Web UI、日志和测试都能复用同一个核心。

## 系统化回答

一个可扩展 Agent Runtime 至少需要这些模块：

1. Provider Adapter：屏蔽不同模型 API 差异。
2. Transcript：维护 user/assistant/tool result 历史。
3. Runtime Loop：处理模型流、工具调用、工具结果和终止条件。
4. Tool Registry：注册工具 schema 和 executor。
5. Policy Engine：判断工具是否允许执行。
6. Event Stream：把过程输出给 UI、日志、评估系统。
7. Middleware：注入 skills、todo、memory、metrics 等横切能力。

## 架构图 / 流程

```text
User Input
  ↓
Runtime Loop
  ↓
Provider Adapter → Model Stream
  ↓
Tool Calls → Policy → Executor → Tool Results
  ↓
Transcript Update
  ↓
RuntimeEvent Stream → TUI / Workbench / Logs / Tests
```

## 关键 trade-off

- 抽象越通用，样板代码越多。
- 事件越细，可观测性越好，但 UI 消费复杂度越高。
- 工具权限越严格，安全性越高，但交互成本可能上升。

## 可靠性与安全

- 设置 max steps 防止无限循环。
- 工具执行支持 abort。
- 高风险工具必须审批。
- 所有工具结果进入 transcript 前可做 redaction 和 summarization。

## 项目例子

- [[30_知识库_Wiki/项目/个人项目/Velaire]]
- [[30_知识库_Wiki/概念/Agent开发/事件驱动Agent Runtime]]

## 高频追问

- 如何处理 streaming 中断？
- 如何支持多个模型 Provider？
- 如何让 UI 实时展示 Agent 过程？
- 如何避免工具调用无限循环？
