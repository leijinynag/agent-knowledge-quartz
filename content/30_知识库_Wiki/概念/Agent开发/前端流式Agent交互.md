---
type: wiki
status: generated
created: 2026-08-12
aliases:
  - Frontend Streaming Agent Interaction
  - SSE POST 流式交互
tags:
  - agent-development
  - frontend
  - sse
  - streaming
confidence: medium
---

# 前端流式Agent交互

## 一句话定义

前端流式 Agent 交互不是简单地把 token 追加到文本框，而是将后端 Runtime 事件解析为可恢复的会话状态，再分别渲染文本、工具、进度、审批和错误。

## EventSource 与 fetch streaming

### EventSource

适合服务端持续推送、请求参数简单的 GET 事件流。它提供原生事件监听和自动重连，但不方便发送 POST body 或自定义 Authorization header。

### fetch streaming

适合创建 Agent run 时使用 POST body、携带自定义 header，或需要手动控制解析、取消、重试和恢复的场景：

```text
fetch POST
  -> response.body.getReader()
  -> TextDecoder
  -> buffer
  -> 按 SSE 事件边界拆分
  -> 解析 event/data/id
  -> 更新 RunState
```

## 事件解析的关键点

- `reader.read()` 返回的是任意字节块，不保证一次对应一个事件。
- UTF-8 字符可能跨 chunk，需要使用 `TextDecoder` 的流式解码能力。
- 必须保留 buffer，直到出现完整事件分隔符。
- 事件可能包含多行 `data:`，不能假设一行就是一个完整 JSON。
- 连接结束前要处理 buffer 中未消费的尾部，并区分正常完成与异常断开。
- 客户端取消后，服务端要停止生成、清理定时器和释放工具资源。

推荐把解析器和 UI 解耦：

```ts
type StreamEvent =
  | { type: 'text_delta'; runId: string; text: string }
  | { type: 'tool_started'; runId: string; toolCallId: string; name: string }
  | { type: 'tool_finished'; runId: string; toolCallId: string; result: unknown }
  | { type: 'approval_required'; runId: string; action: string }
  | { type: 'done'; runId: string }
  | { type: 'error'; runId: string; message: string }
```

## 前端状态机

```ts
type RunState = {
  runId: string
  status: 'connecting' | 'running' | 'waiting_approval' | 'done' | 'error' | 'aborted'
  text: string
  events: StreamEvent[]
  toolCalls: Record<string, { name: string; status: string; result?: unknown }>
  lastEventId?: string
  error?: string
}
```

连接关闭不等于任务完成。前端应以明确的 `done`、`error` 或 `aborted` 事件作为状态终点，并允许通过 `runId` 查询后端最终状态。

## 打字机与渲染性能

- 网络接收频率和 UI 渲染频率分离。
- 先将增量写入 buffer，再按 `requestAnimationFrame` 或固定节奏 flush。
- Markdown 渲染应尽量局部更新，避免每个 token 都全量解析。
- 代码块、链接和未闭合 Markdown 标记要允许中间态。
- 长输出应限制事件列表和 DOM 增长，完整轨迹交给后端或本地日志保存。

## 可靠性清单

- 事件带 `runId` 和递增 `id`，便于去重与恢复。
- 服务端发送心跳，避免代理或负载均衡误判连接空闲。
- 鉴权、CORS、代理缓冲和 gzip 配置要一起验证。
- 重连必须区分“继续消费旧任务”和“重新执行任务”，避免重复副作用。
- 工具调用状态要有超时和取消语义。

## 与现有知识的关系

- Runtime：[[30_知识库_Wiki/概念/Agent开发/事件驱动Agent Runtime]]
- SSE 基础：[[30_知识库_Wiki/概念/Agent开发/SSE流式Agent状态同步]]
- 前端网络：[[30_知识库_Wiki/主题/前端面试/网络面试]]
- 可观测性：[[30_知识库_Wiki/概念/Agent开发/Agent可观测性三支柱与轨迹调试]]

