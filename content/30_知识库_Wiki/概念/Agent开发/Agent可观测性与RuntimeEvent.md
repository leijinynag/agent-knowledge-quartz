---
type: wiki
status: generated
created: 2026-06-26
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

## 事件最小字段

```json
{
  "event_id": "evt_...",
  "trace_id": "trace_...",
  "task_id": "task_...",
  "sequence": 12,
  "type": "tool.completed",
  "timestamp": "...",
  "payload": {},
  "schema_version": 1
}
```

`sequence` 用于恢复顺序，`schema_version` 用于兼容旧日志。事件 ID 要支持去重，避免断线重连后前端重复消费。

## 推荐事件分类

- `task.*`：任务创建、暂停、恢复、取消和完成。
- `model.*`：请求开始、文本增量、usage、结束和错误。
- `tool.*`：请求、校验、审批、执行、结果和失败。
- `state.*`：计划、上下文压缩和 checkpoint。
- `security.*`：权限拒绝、注入检测和敏感数据处理。

业务事件要表达状态变化，而不是直接记录某个 UI 组件应该怎么显示。

## Trace、Log 与 Metric

- Trace 串联一次任务的因果链。
- Log 保存可检索的离散事实。
- Metric 聚合成功率、延迟、成本和错误率。

三者共享 `trace_id`、任务版本、模型版本和工具版本，才能从告警跳到具体失败轨迹。

## 数据治理

- 对工具参数和结果做字段级脱敏。
- 大结果存外部对象，只在事件中放引用和哈希。
- 设置保留周期和租户隔离。
- 审批和副作用事件保留更严格的审计记录。
- 调试界面遵守与业务界面相同的数据权限。

## 回放边界

回放 UI 可以重放事件，但重新执行工具必须使用独立模式。尤其是发送、删除、支付等动作，不能因为点击“回放”再次产生副作用。

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
- [[30_知识库_Wiki/概念/Agent开发/Agent状态管理与Checkpoint]]
- [[30_知识库_Wiki/概念/Agent开发/Agent基准测试与离线评估]]
