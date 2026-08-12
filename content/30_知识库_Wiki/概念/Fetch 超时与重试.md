---
type: wiki
status: generated
created: 2026-06-26
updated: 2026-06-26
aliases:
  - request timeout retry
  - fetch retry
  - AbortController 超时
tags:
  - frontend
  - typescript
  - interview
  - async
  - network
generated_from:
  - "[[10_原始资料_Raw/2026-06-26_本地文件_手撕代码]]"
confidence: medium
---

# Fetch 超时与重试

## 一句话定义

Fetch 超时与重试是在 `fetch` 外包一层请求控制逻辑，用 `AbortController` 实现超时取消，并在失败后按设定次数和间隔重试。

## 核心要点

- `AbortController` 可通过 `signal` 中止 fetch。
- `setTimeout` 到期后调用 `controller.abort()`。
- 无论成功还是失败，都要 `clearTimeout(timer)`。
- 使用 `for` 循环控制最多尝试次数。
- `response.ok` 为 false 时主动抛错。

## 原始实现摘要

原始 TypeScript 实现定义 `RequestOptions extends RequestInit`，增加 `timeout`、`retry`、`retryDelay`。每次尝试时新建 `AbortController`，设置超时定时器，执行 `fetch`，失败时等待 `retryDelay` 后重试，最后一次失败则抛出 `lastError`。

## 推荐实现要点

```ts
interface RequestOptions extends RequestInit {
  timeout?: number
  retry?: number
  retryDelay?: number
}
```

实现重点：

- 每次 retry 都要创建新的 `AbortController`。
- 每次 attempt 都要清理 timer。
- `attempt <= retry` 表示总尝试次数是 `retry + 1`。

## 边界情况

- 是否所有错误都应该重试？例如 4xx 通常不应重试。
- 非幂等请求，如 POST，自动重试可能造成副作用。
- 是否需要指数退避 exponential backoff 和 jitter。
- 如果调用方自己传入 `signal`，需要组合外部 signal 与内部 timeout signal。
- `response.json()` 失败也会进入重试逻辑，是否符合预期需要确认。

## 面试讲解要点

- fetch 本身没有原生 timeout 参数，需要用 abort 实现。
- retry 不是简单重复请求，还要考虑错误类型、幂等性、退避策略。
- `finally` 或重复 `clearTimeout` 都是为了避免 timer 泄漏。

## 与其他概念的关系

- 相关：[[30_知识库_Wiki/概念/Promise 并发控制]]
- 上位主题：[[30_知识库_Wiki/主题/前端手写代码]]

## 来源

- [[10_原始资料_Raw/2026-06-26_本地文件_手撕代码]]

## 待澄清

- 是否需要把这个 request 封装升级成可用于项目的 HTTP client？
