---
type: wiki
status: generated
created: 2026-06-26
aliases:
  - Event-driven Agent Runtime
  - RuntimeEvent
tags:
  - agent-development
  - interview/agent
confidence: medium
---

# 事件驱动Agent Runtime

## 一句话定义

事件驱动 Agent Runtime 是把 Agent 执行过程抽象为一连串可消费事件，让 CLI、TUI、Web UI、日志、测试和回放都能共享同一个执行内核。

## 核心要点

- Runtime 不直接绑定 UI，而是输出 `AsyncIterable<RuntimeEvent>`。
- 事件可以覆盖：模型开始/结束、文本增量、工具调用、工具结果、审批、错误、完成状态等。
- UI 只负责消费事件并渲染，不重新实现 Agent loop。
- 日志和回放也可以基于同一事件协议。

## Runtime 的输入输出

输入不是一段字符串，而是任务合同和依赖：

```ts
run({
  task,
  checkpoint,
  modelGateway,
  toolRegistry,
  policy,
  eventSink,
  signal
}): AsyncIterable<RuntimeEvent>
```

输出事件既服务实时 UI，也写入持久化日志。业务状态由事件驱动更新，但最终快照仍应保存为 checkpoint。

## 事件粒度

粒度过粗时无法解释失败，过细时日志和前端压力过大。通常需要记录状态边界和有业务意义的增量：

- 文本可以按较小批次合并后发送。
- 工具参数只在需要实时预览时发送增量。
- 每次工具状态迁移必须记录。
- token 级内部推理不应默认进入业务日志。

## 顺序与背压

- 每个任务维护单调递增序号。
- 消费方按事件 ID 去重。
- 持久化慢时需要缓冲和上限。
- UI 断线后可从最后序号续传。
- 用户取消通过 `AbortSignal` 传播到模型和工具。

## 错误状态

错误事件至少区分 Provider、工具、权限、上下文、预算和内部状态错误。Runtime 根据错误类型决定重试、重规划、等待用户、降级或终止，而不是统一抛出异常结束进程。

## UI 解耦

CLI、Web 和移动端可以用不同方式呈现同一事件：

- `tool.requested` 在 CLI 显示一行，在 Web 显示确认面板。
- `text.delta` 在终端直接追加，在 Web 进入 Markdown 缓冲。
- `task.waiting` 可以触发通知，而不是保持请求挂起。

## 在 Velaire 中的体现

Velaire 的 CLI/TUI、React Web Workbench、JSONL run log 和测试都围绕 RuntimeEvent 工作。这样可以保证同一个 Agent 核心在不同界面下行为一致。

相关项目：[[30_知识库_Wiki/项目/个人项目/Velaire]]

## 面试表达

> 我没有让 TUI 和 Web UI 各自维护一套 Agent 状态，而是把 Runtime 抽象成事件源。所有 UI 和日志系统消费同一套 RuntimeEvent，这样更容易做调试、回放和可观测性。

## 高频追问

- 事件粒度怎么设计？
- 如何保证事件顺序？
- SSE 和本地 AsyncIterable 怎么映射？
- 工具执行失败如何通过事件表达？

## 相关页面

- [[30_知识库_Wiki/概念/Agent开发/Agent可观测性与RuntimeEvent]]
- [[30_知识库_Wiki/主题/Agent开发/Agent系统设计]]
- [[30_知识库_Wiki/概念/Agent开发/Agent状态管理与Checkpoint]]
- [[30_知识库_Wiki/概念/Agent开发/SSE流式Agent状态同步]]
