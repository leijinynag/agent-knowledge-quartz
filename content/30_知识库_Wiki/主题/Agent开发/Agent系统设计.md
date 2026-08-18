---
type: wiki
status: stable
created: 2026-06-26
aliases:
  - Agent Architecture
  - Agent系统设计
tags:
  - interview/agent
  - agent-architecture
confidence: high
---

# Agent系统设计

## 一句话定义

Agent 系统设计的核心，是用确定性的 Runtime 管住不确定的模型决策，并把目标、状态、工具、权限、事件和验收条件组织成可恢复的执行系统。

## 先定义任务合同

一个模糊目标很难可靠执行。任务开始时至少要明确：

- `goal`：用户最终想得到什么。
- `constraints`：不能违反的范围、权限和格式。
- `success_criteria`：如何判断完成。
- `budget`：时间、轮数、token、费用和工具调用上限。
- `workspace`：允许读写的资源范围。
- `approval_policy`：哪些动作必须等待确认。

任务合同可以由程序和模型共同生成，但最终必须由 Runtime 保存和执行。

## 推荐分层

```text
UI / API
  ↓
Session & Task Service
  ↓
Agent Runtime / State Machine
  ├─ Context Builder
  ├─ Planner / Policy
  ├─ Model Gateway
  ├─ Tool Registry & Executor
  ├─ Permission & Approval
  ├─ Checkpoint Store
  └─ Event / Trace Sink
```

### UI / API

负责输入、进度、取消、审批和结果展示，不重新实现 Agent Loop。

### Task Service

负责会话、任务身份、并发控制、队列、租约和生命周期。

### Runtime

负责状态迁移、循环边界、预算、错误恢复和终止判断。

### Model Gateway

负责 Provider 适配、模型路由、重试、流式事件和 usage。

### Tool Layer

负责能力发现、schema、权限、执行隔离、幂等和结果归一化。

### State 与 Event

State 保存“现在是什么状态”，Event 解释“如何走到这里”。两者结合才能恢复、回放和审计。

## 核心状态机

```text
CREATED
  → RUNNING
      → MODEL_DECIDING
      → TOOL_VALIDATING
      → WAITING_APPROVAL
      → TOOL_EXECUTING
      → OBSERVING
      → VERIFYING
  → COMPLETED
  → FAILED
  → CANCELLED
```

状态迁移必须由 Runtime 驱动。模型可以建议 `final`、`tool_call` 或 `ask_user`，但不能自行修改系统状态。

## Workflow 与 Agent 的组合

生产系统通常不是纯 Agent，而是：

```text
确定性入口
  → 规则路由
  → 受限 Agent 处理开放步骤
  → 确定性校验
  → 人工审批或提交
```

这样既保留模型处理开放问题的能力，又能让关键阶段可预测。

详见：[[30_知识库_Wiki/概念/Agent开发/Workflow与Agent边界]]

## 状态与 Checkpoint

长任务应把关键状态持久化：

- 当前步骤与计划版本。
- 已完成子目标。
- 工具调用及其幂等键。
- 关键事实与证据位置。
- 已获得的用户确认。
- 当前预算和取消状态。

恢复时不能简单重放全部消息，应从 checkpoint 读取状态，再验证外部世界是否已经变化。

详见：

- [[30_知识库_Wiki/概念/Agent开发/Agent状态管理与Checkpoint]]
- [[30_知识库_Wiki/概念/Agent开发/Agent持久化执行与断点恢复]]

## 并发和长任务

需要明确：

- 同一任务是否只允许一个 worker 持有执行租约。
- 用户取消如何传播到模型和工具。
- 任务重试是否复用原任务 ID。
- 多个工具调用如何聚合结果。
- 服务重启后如何找到未完成任务。
- 用户离开页面后任务是否继续。

对长任务，队列负责调度，checkpoint 负责恢复，事件流负责通知，不能依赖一条持续数分钟的 HTTP 连接维持状态。

## 完成验证

模型说“完成”只是候选终态。Runtime 应使用：

- 测试是否通过。
- 文件或数据是否真实存在。
- 输出是否满足 schema。
- 必要步骤是否全部完成。
- 是否存在未解决错误。
- 是否违反权限或预算。

高价值任务可使用独立 verifier，但 verifier 也不能替代确定性检查。

## 常见失败模式

- UI、Runtime 和工具层各自保存一份互相冲突的状态。
- 只保存对话，不保存任务结构和工具副作用。
- 服务重启后从头执行，重复发送或写入。
- 模型自行判断完成，没有外部验收。
- 所有工具一直暴露，导致选择不稳定。
- 错误只记录堆栈，没有业务错误分类和恢复动作。
- 多 Agent 共享完整上下文，成本和污染快速放大。

## 系统设计题答题顺序

1. 明确用户、任务和成功标准。
2. 判断 Workflow 还是 Agent。
3. 列出工具与风险等级。
4. 画出 Runtime、状态和事件。
5. 说明上下文、记忆和检索。
6. 说明超时、重试、取消和恢复。
7. 说明权限、审批和隔离。
8. 说明评估、trace、成本与降级。

## 面试表达

我会让 Runtime 成为控制平面，模型只是策略生成器。任务有明确合同，工具调用要经过校验和权限，状态通过 checkpoint 持久化，过程通过事件记录，最终由验收器确认。开放步骤可以使用 Agent，但外层阶段、权限和提交动作保持确定性。

## 相关页面

- [[30_知识库_Wiki/概念/Agent开发/Agent Loop与终止条件]]
- [[30_知识库_Wiki/概念/Agent开发/Agent系统组件边界]]
- [[30_知识库_Wiki/概念/Agent开发/事件驱动Agent Runtime]]
- [[30_知识库_Wiki/概念/Agent开发/Agent生产故障分类与运行手册]]
