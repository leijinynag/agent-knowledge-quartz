---
type: wiki
status: generated
created: 2026-06-26
aliases:
  - SSE Agent 状态同步
  - Server-Sent Events Agent Streaming
  - EventSource
  - 流式输出
tags:
  - agent-development
  - frontend
  - sse
  - network
  - interview
confidence: medium
---

# SSE流式Agent状态同步

## 一句话定义

SSE（Server-Sent Events）是一种基于 HTTP 长连接的服务端单向推送机制，适合把 LLM / Agent 的流式输出、工具调用、任务进度、审批等待和最终状态持续推送给前端。

## 30秒面试回答

SSE 适合服务端持续向前端推送事件，比如 AI 流式输出、任务进度和日志流。它基于普通 HTTP，浏览器有原生 `EventSource` 支持，协议简单、自动重连、比 WebSocket 更轻。但它是单向的，如果需要客户端频繁实时发送消息，WebSocket 更合适。Agent 场景下我会把后端 Runtime 事件转换成 SSE 事件，前端维护一个 session/run 状态机，而不是只拼接字符串。

---

## 为什么 SSE 适合 LLM / Agent 场景

Agent 任务通常不是一次请求一次响应，而是一个持续过程：

- 模型 token 增量输出
- 工具调用开始 / 结束
- 工具结果返回
- 审批等待
- 任务进度变化
- 错误恢复
- 多轮循环
- 最终完成

SSE 可以把这些过程拆成稳定事件流，前端按事件类型更新 UI。

```text
Agent Runtime
  → RuntimeEvent
  → SSE Event
  → Frontend Session State
  → Conversation / Timeline / Tool Panel / Typewriter UI
```

相关：[[30_知识库_Wiki/概念/Agent开发/事件驱动Agent Runtime]]、[[30_知识库_Wiki/概念/Agent开发/Agent可观测性与RuntimeEvent]]

---

## SSE 协议基础

SSE 响应的核心 Header：

```http
Content-Type: text/event-stream
Cache-Control: no-cache, no-transform
Connection: keep-alive
```

事件格式：

```text
event: message_delta
id: 123
data: {"text":"你好"}

```

字段说明：

| 字段 | 作用 |
|---|---|
| `event` | 事件类型，前端可按类型监听 |
| `data` | 事件内容，通常是 JSON 字符串 |
| `id` | 事件 ID，用于断线续传或幂等处理 |
| `retry` | 建议浏览器重连间隔，单位 ms |
| 空行 | 表示一个事件结束 |

心跳事件常见写法：

```text
: ping

```

以冒号开头的是注释，不会触发 message 事件，但能保持连接活跃。

---

## SSE、WebSocket、HTTP Streaming、轮询对比

| 方案                             | 方向          | 优点                                   | 缺点                                          | 适合场景                              |
| ------------------------------ | ----------- | ------------------------------------ | ------------------------------------------- | --------------------------------- |
| SSE                            | 服务端 → 客户端单向 | 简单、基于 HTTP、浏览器原生支持、自动重连              | 只能单向；原生 EventSource 不方便自定义 POST body/header | AI 流式输出、进度、通知、日志                  |
| WebSocket                      | 双向          | 真双向、低延迟、适合频繁交互                       | 协议和状态更复杂；心跳、重连、鉴权都要设计                       | 聊天、协同编辑、在线状态、游戏                   |
| HTTP chunked / fetch streaming | 服务端 → 客户端   | 可用 POST、自定义 header、可读 ReadableStream | 需要手动解析流和重连                                  | LLM token streaming、需要带复杂请求体的流式接口 |
| 轮询                             | 客户端定时请求     | 实现简单、兼容性好                            | 延迟高、浪费请求                                    | 低频状态查询                            |
| 长轮询                            | 客户端请求挂起     | 比轮询实时                                | 服务端连接管理复杂                                   | 兼容性兜底                             |

## 如何选择

### 优先用 SSE 的场景

- 服务端持续推送，客户端只是偶尔发起请求。
- AI 文本流式输出。
- Agent timeline / tool status / job progress。
- 日志流、通知流。
- 希望实现简单，并复用 HTTP 基础设施。

### 优先用 WebSocket 的场景

- 双向频繁通信。
- 聊天室、实时协作、多人在线状态。
- 客户端也要高频发送事件。
- 需要较低延迟和更细的连接控制。

### 优先用 fetch streaming 的场景

- 需要用 POST 发送复杂 body。
- 需要自定义 Authorization header，且不想用 cookie。
- 想自己控制重连、解析和 backpressure。

> 面试表达：SSE 不是 WebSocket 的替代品，它适合“服务端持续推送为主”的场景。Agent/LLM 输出就是典型场景，因为用户发起一次任务后，后端持续推送执行过程。

---

## 前端如何使用 SSE

### EventSource 基础用法

```js
const es = new EventSource('/api/agent/runs/123/events')

es.addEventListener('message_delta', (event) => {
  const data = JSON.parse(event.data)
  appendText(data.text)
})

es.addEventListener('tool_call', (event) => {
  const data = JSON.parse(event.data)
  updateToolStatus(data)
})

es.addEventListener('done', () => {
  es.close()
})

es.onerror = () => {
  // EventSource 会自动重连，但这里可以更新 UI 状态
  setConnectionState('reconnecting')
}
```

### 前端状态不应只拼接字符串

Agent 场景至少要维护这些状态：

```ts
type RunState = {
  runId: string
  status: 'connecting' | 'running' | 'waiting_approval' | 'done' | 'error' | 'aborted'
  text: string
  events: RuntimeEvent[]
  toolCalls: Record<string, ToolCallState>
  lastEventId?: string
  error?: string
}
```

原因：

- 工具调用和文本输出是不同 UI 区域。
- 任务可能等待用户确认。
- 连接可能断开但任务仍在后端运行。
- 最终状态必须明确，不能只靠连接关闭判断完成。

### 原生 EventSource 的限制

原生 `EventSource`：

- 默认发 GET 请求。
- 不能直接设置自定义 header。
- 可以携带 cookie：`new EventSource(url, { withCredentials: true })`。
- 支持 `Last-Event-ID` 自动重连语义。

如果你需要 POST body 或 Authorization header，可以考虑：

- 先 POST 创建 run，返回 `runId`，再用 EventSource GET `/runs/:id/events`。
- 或使用 fetch streaming 自己解析 SSE。

---

## 前端打字机效果要考虑什么

AI 产品中“打字机效果”不只是把 token 立刻 append 到 DOM。要考虑用户体验、性能、Markdown、代码块和状态一致性。

### 1. 数据流和渲染节奏分离

后端 token 到达频率可能很高，不应每个 token 都触发 React setState 和 Markdown 全量渲染。

建议：

```text
SSE chunk 到达 → 写入 buffer → requestAnimationFrame / 定时 flush → 更新 UI
```

示例：

```js
let buffer = ''
let scheduled = false

function onDelta(text) {
  buffer += text
  if (!scheduled) {
    scheduled = true
    requestAnimationFrame(() => {
      appendVisibleText(buffer)
      buffer = ''
      scheduled = false
    })
  }
}
```

### 2. Markdown 增量渲染问题

LLM 输出经常包含 Markdown：

- 代码块 ``` 可能尚未闭合。
- 表格可能还没输出完整。
- 列表可能断在中间。
- 数学公式可能不完整。

策略：

- 简单场景：流式期间用纯文本/轻量 Markdown，完成后再完整渲染。
- 复杂场景：使用支持增量容错的 Markdown 渲染策略。
- 代码块：未闭合时仍以临时代码块样式展示，done 后再高亮。

### 3. 自动滚动

需要区分用户是否正在查看历史：

- 如果用户在底部，自动滚动。
- 如果用户手动向上滚动，暂停自动滚动，显示“有新内容”按钮。

### 4. 光标和 loading 状态

- 文本流式输出时显示 cursor。
- 工具调用阶段可能没有文本输出，应显示“正在调用工具/分析文件”。
- 结束事件到达后移除 cursor。

### 5. 多消息并发

Agent 可能同时有：

- assistant text
- tool call card
- tool result
- approval card
- error card

不要把所有东西都混成一个字符串。推荐 conversation + timeline 双结构。

### 6. 性能优化

- 批量 setState，避免每 token setState。
- 大文本使用虚拟列表或分段渲染。
- Markdown 渲染做 debounce / idle callback。
- 代码高亮延迟到代码块完成或任务结束。
- 长对话只渲染可视区域。

---

## 后端如何实现 SSE

### Node / Express 示例

```js
app.get('/api/runs/:id/events', async (req, res) => {
  res.setHeader('Content-Type', 'text/event-stream')
  res.setHeader('Cache-Control', 'no-cache, no-transform')
  res.setHeader('Connection', 'keep-alive')
  res.flushHeaders?.()

  const send = (event, data, id) => {
    if (id) res.write(`id: ${id}\n`)
    res.write(`event: ${event}\n`)
    res.write(`data: ${JSON.stringify(data)}\n\n`)
  }

  const heartbeat = setInterval(() => {
    res.write(': ping\n\n')
  }, 15000)

  req.on('close', () => {
    clearInterval(heartbeat)
    // 清理订阅，但不一定取消后端任务
  })

  send('connected', { runId: req.params.id })
})
```

### 后端事件设计

建议定义稳定事件类型：

| 事件 | 说明 |
|---|---|
| `connected` | 连接建立 |
| `message_start` | assistant 消息开始 |
| `message_delta` | 文本增量 |
| `message_done` | assistant 消息结束 |
| `tool_call_start` | 工具调用开始 |
| `tool_call_delta` | 工具调用参数增量，可选 |
| `tool_call_done` | 工具调用参数完成 |
| `tool_result` | 工具结果 |
| `approval_required` | 等待用户确认 |
| `progress` | 任务进度 |
| `error` | 错误 |
| `done` | run 结束 |
| `aborted` | run 被取消 |

### 事件 envelope

建议统一包一层 envelope：

```ts
type SseEnvelope<T> = {
  id: string
  runId: string
  type: string
  ts: number
  data: T
}
```

好处：

- 方便幂等。
- 方便日志和回放。
- 方便断线恢复。
- 方便前端统一 reducer。

### 后端需要注意的 Header / 代理问题

- `Content-Type: text/event-stream`
- `Cache-Control: no-cache, no-transform`
- 关闭代理 buffering，例如 Nginx：`X-Accel-Buffering: no`
- 避免 gzip 压缩导致小块不及时 flush。
- 定期 heartbeat，避免连接被中间层断开。
- 每次 `res.write` 后不要立刻结束响应。
- 任务结束时发送明确 `done` 事件，再关闭连接。

---

## 前后端协议设计建议

### 推荐流程

```text
1. 前端 POST /api/runs 创建任务
2. 后端返回 runId
3. 前端 EventSource GET /api/runs/:runId/events
4. 后端持续推送事件
5. 前端 reducer 更新 RunState
6. done/error/aborted 后关闭连接
```

为什么不直接 EventSource 带所有参数？

- EventSource 主要是 GET。
- 复杂任务参数放 URL 不合适。
- 权限、幂等、任务恢复更适合先创建 run。

### 推荐事件 JSON

```json
{
  "id": "evt_001",
  "runId": "run_123",
  "type": "message_delta",
  "ts": 1710000000000,
  "data": {
    "messageId": "msg_1",
    "text": "hello"
  }
}
```

### 前端 reducer 示例

```ts
function reduceRun(state, event) {
  switch (event.type) {
    case 'message_delta':
      return { ...state, text: state.text + event.data.text }
    case 'tool_call_start':
      return addToolCall(state, event.data)
    case 'approval_required':
      return { ...state, status: 'waiting_approval' }
    case 'done':
      return { ...state, status: 'done' }
    case 'error':
      return { ...state, status: 'error', error: event.data.message }
    default:
      return state
  }
}
```

---

## 断线重连、幂等和恢复

### EventSource 自动重连

浏览器 EventSource 会自动重连，并携带 `Last-Event-ID`。但前提是后端发送了 `id:`。

### 后端如何支持恢复

需要保存最近事件：

- 内存 ring buffer：适合短任务。
- Redis Stream：适合多实例和较长任务。
- 数据库 / JSONL：适合审计和回放。

恢复流程：

```text
前端断线
  → EventSource 重连并带 Last-Event-ID
  → 后端查找该 ID 之后的事件
  → 补发遗漏事件
  → 继续订阅实时事件
```

### 幂等处理

前端应记录已处理 `event.id`，避免重复应用：

```js
const seen = new Set()
function handleEvent(event) {
  if (seen.has(event.id)) return
  seen.add(event.id)
  dispatch(event)
}
```

### 任务取消

SSE 连接断开不一定代表任务取消。

建议区分：

- 用户关闭页面：只断开连接，任务可继续。
- 用户点击取消：调用 `POST /runs/:id/cancel`，后端 abort 任务并发送 `aborted`。

---

## 边界情况清单

### 前端边界

- 连接成功但长时间无事件：显示 heartbeat / still running 状态。
- 收到重复事件：按 event id 去重。
- 断线重连后漏事件：依赖 Last-Event-ID 补发。
- 用户滚动离开底部：暂停自动滚动。
- Markdown 未闭合：流式期间容错渲染。
- 超长输出：虚拟列表、分段渲染。
- 工具调用没有文本输出：显示工具状态，而不是空白 loading。
- 错误事件到达后：展示错误，并关闭连接或允许重试。

### 后端边界

- 客户端断开：清理订阅和定时器。
- 中间代理 buffering：关闭缓冲。
- 长连接超时：heartbeat。
- 多实例部署：事件存储不能只依赖单机内存。
- 任务结束但连接未关闭：必须发送 `done` 并 close。
- 上游模型流中断：发送 error 或 retrying 事件。
- 工具结果过大：摘要展示 + 提供详情文件/分页获取。
- 用户审批等待：发送明确 `approval_required`，暂停 Agent loop。

---

## 性能优化

### 前端性能

- 批量更新 UI，不要每 token setState。
- 用 `requestAnimationFrame` 或 30-60ms throttle flush 文本 buffer。
- Markdown 渲染分层：流式轻渲染，完成后完整渲染。
- 代码高亮延迟执行。
- 长消息分块存储，避免一个超大字符串频繁复制。
- 长会话用虚拟列表。
- Timeline 和正文分开渲染，避免工具事件导致正文重排。

### 后端性能

- 不要每个 token 都做重 JSON 序列化和数据库写入；可按事件类型或批量写日志。
- 心跳间隔合理，例如 15-30s。
- 对慢客户端处理 backpressure，避免内存无限堆积。
- 多实例下使用 Redis Pub/Sub、Redis Stream 或消息队列承接事件。
- 工具大结果不要直接塞入 SSE，可存文件/对象存储后推送引用。
- 控制并发连接数和单用户 run 数量。

### 网络和代理

- 禁止代理缓冲。
- 注意 CDN / 网关对长连接的超时限制。
- 避免对 SSE 做 gzip 小块压缩。
- 如果企业代理不稳定，需要降级方案：轮询或 fetch streaming。

---

## 安全与鉴权

### EventSource 鉴权

因为原生 EventSource 不方便加自定义 header，常见方案：

1. Cookie + SameSite + CSRF 防护。
2. URL 中放短期一次性 token，不要放长期 token。
3. 先 POST 创建 run，服务端校验权限，再允许订阅该 run 的事件。

### 权限校验

后端必须校验：

- 当前用户是否拥有该 `runId`。
- 是否允许查看该 session 的事件。
- 是否允许发送 cancel / approval 操作。

### 隐私和日志

SSE 事件可能包含：

- 用户输入
- 模型输出
- 工具结果
- 文件路径
- 错误堆栈

需要考虑 redaction 和日志脱敏。

相关：[[30_知识库_Wiki/主题/Agent开发/Agent安全与权限]]

---

## 在 Agent 产品中的推荐架构

```text
Frontend
  ├─ createRun(prompt)
  ├─ subscribeRunEvents(runId)
  ├─ runState reducer
  ├─ typewriter renderer
  ├─ timeline renderer
  └─ approval/cancel actions

Backend
  ├─ Run manager
  ├─ Agent Runtime
  ├─ Tool executor
  ├─ Event bus
  ├─ Event store
  └─ SSE controller
```

关键原则：

- Agent Runtime 产出内部事件。
- Event bus 负责广播。
- Event store 负责恢复和审计。
- SSE controller 只负责协议输出。
- 前端 reducer 负责状态更新。

---

## 面试回答模板

### SSE 和 WebSocket 怎么选？

> 我会先看通信方向。如果主要是服务端持续推送，比如 AI 输出、任务进度、日志流，SSE 更简单，基于 HTTP，浏览器原生支持自动重连；如果客户端和服务端都要高频实时发送，比如聊天、协同编辑、在线状态，我会选 WebSocket。Agent 输出一般是用户发起任务后服务端持续推送过程，所以 SSE 很适合。

### 前端打字机效果怎么做？

> 我不会每个 token 都 setState，而是把流式 delta 先写入 buffer，再用 requestAnimationFrame 或定时器批量 flush 到 UI。流式期间 Markdown 和代码高亮要容错，代码块没闭合时不能导致渲染崩溃；长文本要分段或虚拟化，自动滚动也要尊重用户是否正在查看历史。

### 后端 SSE 要注意什么？

> 后端要设置 `text/event-stream`、关闭缓存和代理缓冲，定期 heartbeat，客户端断开时清理资源。更重要的是要发送稳定事件 ID，支持 Last-Event-ID 断线恢复；任务结束时必须发送明确 done/error/aborted，而不是让前端通过连接关闭猜状态。

---

## 高频追问

- SSE 为什么比轮询更适合 AI 输出？
- SSE 和 WebSocket 的本质区别是什么？
- EventSource 为什么不方便带 Authorization header？
- 如何实现断线续传？
- Last-Event-ID 怎么用？
- 打字机效果如何避免 React 频繁渲染？
- Markdown 流式输出如何处理未闭合代码块？
- 后端如何处理客户端断开？
- 多实例部署时 SSE 事件怎么广播？
- 工具结果很大时要不要直接通过 SSE 推？

## 相关页面

- [[30_知识库_Wiki/项目/个人项目/Velaire]]
- [[30_知识库_Wiki/主题/Agent开发/Agent系统设计]]
- [[30_知识库_Wiki/主题/Agent开发/LLM应用开发]]
- [[30_知识库_Wiki/主题/前端面试/网络面试]]
- [[30_知识库_Wiki/概念/前端工程化/WebSocket在线状态与实时通信]]
- [[30_知识库_Wiki/概念/Agent开发/事件驱动Agent Runtime]]
- [[30_知识库_Wiki/概念/Agent开发/Agent可观测性与RuntimeEvent]]
