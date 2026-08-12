---
type: wiki
status: generated
created: 2026-08-12
updated: 2026-08-12
aliases:
  - Durable Agent Execution
  - Agent断点恢复
  - Agent持久化Runtime
tags:
  - interview/agent
  - agent-runtime
  - durability
  - event-sourcing
confidence: medium
---

# Agent持久化执行与断点恢复

## 为什么 Agent 不能只放在一个请求里

只要任务可能运行几分钟、等待用户审批、遇到网络故障，单个 HTTP 请求就不够用了。进程重启、浏览器关闭或 worker 崩溃后，Agent 还应该知道自己做到哪里。

所以我会把一次执行拆成四层：

```text
run      一次完整任务
turn     一轮模型决策
event    发生过的事实
checkpoint 当前可恢复快照
```

## Checkpoint 保存什么

至少要保存：

- 用户目标和验收条件。
- 当前计划及每一步状态。
- 已确认的事实和关键工具结果。
- 尚未完成的工具调用。
- token、时间、调用次数等预算。
- 当前运行状态和取消标记。
- 模型、prompt、tool schema 和知识快照版本。

最后一项经常被忽略。代码更新后，如果用新的工具 schema 去恢复旧任务，可能会得到无法解释的行为。

## Event Log + Snapshot

我更倾向于“事件日志加快照”：

- 事件日志保存发生过什么，适合审计和回放。
- checkpoint 保存当前状态，恢复时不用从第一步重放所有事件。
- 快照损坏或需要排查时，可以用事件日志重建状态。

典型事件包括：

```text
RunStarted
PlanUpdated
ToolCallRequested
ToolCallStarted
ToolCallFinished
ApprovalRequested
ApprovalResolved
StateChanged
RunPaused
RunCompleted
RunFailed
```

## 恢复时最危险的问题

如果 checkpoint 显示“准备调用发送邮件”，但 worker 在真正发送后、写入结果前崩溃，恢复后不能简单地再次发送。

我会根据工具契约处理：

1. 有幂等键：使用同一个 key 重试。
2. 能查状态：先查询动作是否已经完成。
3. 能回滚：执行补偿。
4. 都不能保证：暂停并请求人工处理。

恢复的核心不是“从上次文本继续”，而是避免重复副作用并重新确认外部状态。

## Worker ownership

长任务通常需要 lease 和 heartbeat：

- worker 拿到 lease 后才允许推进 run。
- 定期 heartbeat，表明自己还活着。
- lease 过期后其他 worker 才能接管。
- 同一个 run 同时只能有一个有效 owner。

否则两个 worker 可能同时执行同一工具，产生重复动作。

## 状态机

```text
queued -> running -> waiting_approval -> running
                     |                 |
                     v                 v
                  cancelled         completed
                                       |
                                       v
                                     failed
```

状态迁移要有明确规则，终态不能被普通事件重新改回 `running`。用户取消也不只是前端按钮变灰，Runtime 和底层工具都要收到取消信号。

## 面试表达

长运行 Agent 我会持久化 run、turn、event 和 checkpoint。事件用于审计与回放，快照用于快速恢复；恢复副作用工具时优先复用幂等键或查询外部状态，不能直接从最后一条文本继续。再通过 lease、heartbeat、版本信息和终态状态机，保证任务不会被两个 worker 重复推进。

## 相关笔记

- [[30_知识库_Wiki/概念/Agent开发/事件驱动Agent Runtime]]
- [[30_知识库_Wiki/概念/Agent开发/Agent可观测性与RuntimeEvent]]
- [[30_知识库_Wiki/概念/Agent开发/Agent工具执行的幂等重试与超时]]
- [[30_知识库_Wiki/概念/Agent开发/前端流式Agent交互]]
- [[30_知识库_Wiki/概念/Agent开发/Agent Loop与终止条件]]

