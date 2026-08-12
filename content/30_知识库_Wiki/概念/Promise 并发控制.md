---
type: wiki
status: generated
created: 2026-06-26
updated: 2026-06-26
aliases:
  - batchTask
  - Promise concurrency limit
  - 并发函数
tags:
  - frontend
  - javascript
  - interview
  - async
generated_from:
  - "[[10_原始资料_Raw/2026-06-26_本地文件_手撕代码]]"
confidence: medium
---

# Promise 并发控制

## 一句话定义

Promise 并发控制是在任务列表中同时只运行固定数量的异步任务，并在全部完成后按原顺序返回结果。

## 核心要点

- 用共享 `index` 分发任务。
- 创建固定数量的 worker。
- 每个 worker 循环领取下一个任务。
- 用 `result[currentIndex]` 保持结果顺序。
- 用 `Promise.all(workers)` 等待所有 worker 完成。

## 原始实现摘要

原始目录中有两个近似重复实现：`并发函数.js` 和 `并发函数v2.js`。二者都实现了 worker-pool 模式，差别主要是参数名和结果数组名：`num/result` 与 `limit/res`。

## 推荐实现要点

```js
async function batchTask(tasks, limit) {
  const result = []
  let index = 0

  async function worker() {
    while (index < tasks.length) {
      const currentIndex = index++
      result[currentIndex] = await tasks[currentIndex]()
    }
  }

  const workers = new Array(limit).fill(0).map(() => worker())
  await Promise.all(workers)
  return result
}
```

## 边界情况

- `limit <= 0` 时应如何处理？通常应抛错或归一化为 1。
- 单个任务 reject 时，是 fail-fast，还是收集所有错误？
- `tasks` 是函数数组还是 Promise 数组？当前实现要求是函数数组。
- 是否需要中途取消？
- 是否需要进度回调？

## 面试讲解要点

- 关键是“多个 worker 共享同一个自增索引”。
- 保存 `currentIndex` 是为了结果顺序不被完成时间打乱。
- `Promise.all` 会在任一 worker reject 时整体 reject，这是默认 fail-fast 策略。

## 与其他概念的关系

- 相关：[[30_知识库_Wiki/概念/Fetch 超时与重试]]
- 上位主题：[[30_知识库_Wiki/主题/前端手写代码]]

## 来源

- [[10_原始资料_Raw/2026-06-26_本地文件_手撕代码]]

## 待澄清

- 任务失败时，用户希望 fail-fast 还是收集全部错误后统一返回？
