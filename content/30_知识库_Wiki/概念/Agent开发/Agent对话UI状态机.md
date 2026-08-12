---
type: wiki
status: generated
created: 2026-08-12
updated: 2026-08-12
aliases:
  - Agent Chat UI State Machine
  - Agent对话状态机
  - AI对话UI状态
tags:
  - interview/agent
  - frontend
  - agent-ui
  - state-machine
confidence: medium
---

# Agent对话UI状态机

## 我的理解

Agent 对话 UI 不应该只维护一个 `messages` 数组。普通聊天可以只关心“用户说了什么、助手回了什么”，但 Agent 还会规划、调工具、等待确认、失败重试、恢复运行。

所以前端真正要维护的是一个 run 状态机，消息只是其中一种投影。

## 我会拆成这些状态

```ts
type RunStatus =
  | "idle"
  | "submitting"
  | "connecting"
  | "running"
  | "waiting_approval"
  | "reconnecting"
  | "completed"
  | "failed"
  | "cancelled"
```

这些状态不能混在一个 `loading: boolean` 里。`running` 和 `waiting_approval` 都是“还没结束”，但 UI 行为完全不同：前者可以显示生成中，后者要给用户一个明确操作入口。

## 消息、事件、任务状态分开

我会分三层：

```ts
type AgentSession = {
  runId: string
  status: RunStatus
  messages: ChatMessage[]
  timeline: RuntimeEvent[]
  toolCalls: Record<string, ToolCallView>
  pendingApproval?: ApprovalView
}
```

- `messages` 用于对话区。
- `timeline` 用于调试、轨迹、回放。
- `toolCalls` 用于工具面板。
- `pendingApproval` 用于确认动作。

这样 UI 不会被一堆特殊消息污染，比如把“正在读取文件”“需要确认删除”都硬塞进聊天气泡里。

## 事件归并

后端可能发很多细碎事件，前端不需要每个事件都渲染成一条消息。

我会做一层 reducer：

```text
RuntimeEvent -> sessionReducer -> AgentSession -> UI
```

例如多个 `text_delta` 归并成同一条 assistant 消息；`tool_started` 和 `tool_finished` 更新同一个工具卡片；`approval_required` 切换到等待确认态。

## UI 上的几个判断

- 连接断了不等于任务失败，要看后端 run 状态。
- 用户点停止后，要显示 cancelled，而不是 failed。
- 工具失败但 Agent 还能恢复时，不要过早把整轮标红。
- 最终消息完成后，也要等待明确的 `completed` 事件。
- 重新连接时要按 `lastEventId` 或后端快照恢复，不要重新提交用户问题。

## 常见失败方式

- 一个 `isLoading` 管所有状态，最后按钮、输入框、错误提示全混乱。
- 每个事件都 append 成一条聊天消息，界面变成日志流。
- 连接关闭就直接显示完成，实际后端还在跑。
- 刷新页面后丢失 runId，只能重新执行，可能触发重复副作用。
- 工具调用状态不归并，用户看不懂 Agent 到底做到了哪一步。

## 面试表达

我会把 Agent 对话 UI 当成状态机做，而不是普通聊天列表。后端发 RuntimeEvent，前端 reducer 归并成 session state，再分别渲染对话、工具、进度、审批和错误。这样连接恢复、取消、等待确认和工具失败都能被表达清楚。

## 相关笔记

- [[30_知识库_Wiki/概念/Agent开发/前端流式Agent交互]]
- [[30_知识库_Wiki/概念/Agent开发/SSE流式Agent状态同步]]
- [[30_知识库_Wiki/概念/Agent开发/Agent可观测性与RuntimeEvent]]
- [[30_知识库_Wiki/概念/Agent开发/Agent风险分级与人工审批]]

