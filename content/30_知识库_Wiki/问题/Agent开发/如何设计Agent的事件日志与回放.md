---
type: wiki
status: generated
created: 2026-06-26
aliases:
  - Agent事件日志设计题
  - Agent replay设计题
tags:
  - interview/question
  - agent-development
  - observability
confidence: medium
---

# 问题：如何设计Agent的事件日志与回放？

## 30秒回答

我会把 Agent 执行过程记录成结构化事件流，而不是只存最终回答。事件包括模型请求、文本增量、工具调用、工具结果、审批、错误、usage 和终止原因。这样可以支持 UI timeline、问题排查、成本分析和离线回放。

## 系统化回答

事件日志至少需要记录：

- session / run id
- user message
- model request start / end
- assistant text delta
- tool call
- policy decision
- approval request / result
- tool result
- error
- final status
- token usage / latency

## 架构图 / 流程

```text
RuntimeEvent Stream
  ├─ TUI 渲染
  ├─ Web Workbench Timeline
  ├─ JSONL Run Log
  ├─ Metrics Aggregation
  └─ Replay / Debug
```

## 关键 trade-off

- 事件越细，调试越方便，但日志体积越大。
- 工具结果可能包含敏感信息，需要 redaction。
- replay 要区分“真实重放”和“基于日志回看”。

## 项目例子

- [[30_知识库_Wiki/项目/个人项目/Velaire]]
- [[30_知识库_Wiki/概念/Agent开发/Agent可观测性与RuntimeEvent]]

## 高频追问

- JSONL 日志如何设计？
- 如何保护日志中的 secret？
- 如何统计 Agent 成功率？
- 如何用事件日志定位一次失败任务？

## 相关页面

- [[30_知识库_Wiki/主题/Agent开发/Agent评估与可观测性]]
- [[30_知识库_Wiki/概念/Agent开发/事件驱动Agent Runtime]]
