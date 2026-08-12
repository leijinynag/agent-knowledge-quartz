---
type: wiki
status: generated
created: 2026-08-12
updated: 2026-08-12
aliases:
  - Agent Tool Reliability
  - Agent工具重试
  - Agent幂等执行
tags:
  - interview/agent
  - agent-runtime
  - tool-use
  - reliability
confidence: medium
---

# Agent工具执行的幂等重试与超时

## 我为什么会单独设计这一层

Agent 很容易把“工具失败”理解成“再调用一次就好”，但工具调用可能已经成功，只是结果在网络返回时丢了。

如果这个工具是发邮件、扣库存、提交代码或创建订单，盲目重试就可能产生重复副作用。所以工具执行不能只依赖模型判断，Runtime 需要自己管理重试、超时和取消。

## 一次调用至少要带什么

```ts
type ToolExecution = {
  toolCallId: string
  idempotencyKey: string
  deadline: number
  attempt: number
  signal: AbortSignal
}
```

- `toolCallId` 用来关联模型轨迹。
- `idempotencyKey` 用来让服务端识别同一个业务动作。
- `deadline` 比单纯的 timeout 更适合跨多层调用传递剩余时间。
- `attempt` 方便限次和排查。
- `signal` 用于用户取消和上游超时的传播。

## 哪些错误可以重试

通常可以考虑重试：

- 临时网络错误。
- 请求超时，但服务端明确没有接受请求。
- 限流。
- 依赖服务短暂不可用。

不应该自动重试：

- 参数校验失败。
- 权限不足。
- 资源不存在。
- 业务规则拒绝。
- 副作用是否已完成无法判断。

最后一种最麻烦。我的默认处理是先查询动作状态，再决定是否补偿，而不是直接再发起一次。

## 重试策略

常见公式：

```text
delay = min(maxDelay, base * 2^attempt) + randomJitter
```

重试必须同时受这些限制：

- 最大尝试次数。
- 总 deadline。
- 单个工具自己的预算。
- 当前任务剩余预算。

遇到持续失败的依赖，可以加 circuit breaker，避免所有 Agent 同时把故障服务打得更严重。对不同租户或不同工具分开限流，也比全局共享一个队列更容易控制。

## 幂等比 exactly-once 更现实

分布式系统里很难保证“只执行一次并且一定拿到结果”。实际工程通常接受 at-least-once，然后用幂等键、业务唯一约束和状态查询把重复执行变成安全操作。

例如创建任务时，服务端可以按：

```text
tenantId + idempotencyKey
```

保存第一次执行结果。相同 key 的后续请求直接返回同一个结果。

## 工具契约最好明确写出来

工具 schema 除了参数，还应该说明：

- 是否有副作用。
- 是否支持幂等。
- 哪些错误可重试。
- 默认超时时间。
- 是否需要人工确认。
- 成功后的可查询状态。

这样模型和 Runtime 都不需要猜。

## 常见失败方式

- 所有错误都重试。
- 只在客户端生成幂等 key，服务端完全不认。
- 每层都有独立 timeout，最后总耗时远超用户预期。
- 用户取消后，底层请求还在继续产生副作用。
- 重试事件没有记录，最后无法解释成本为什么暴涨。

## 面试表达

我不会让 Agent 自己决定是否无限重试。Runtime 会根据工具契约区分可重试错误和业务错误，给每次调用传递 deadline、取消信号和幂等键。对于可能已经成功但响应丢失的副作用动作，先查状态或走补偿流程，不能盲目重复执行。

## 相关笔记

- [[30_知识库_Wiki/主题/Agent开发/Tool Use与函数调用]]
- [[30_知识库_Wiki/概念/Agent开发/Capability-based Tool Policy]]
- [[30_知识库_Wiki/概念/Agent开发/Agent可观测性与RuntimeEvent]]
- [[30_知识库_Wiki/概念/Agent开发/Agent Loop与终止条件]]

