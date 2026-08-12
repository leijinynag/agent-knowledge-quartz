---
type: wiki
status: draft
created: 2026-06-26
updated: 2026-07-11
aliases:
  - Promise
  - async await
  - Promise面试
tags:
  - frontend/javascript
  - interview/frontend
  - async
generated_from: []
confidence: medium
---

# Promise与async-await

## 30秒回答

Promise 是 JavaScript 处理异步结果的对象，有 `pending`、`fulfilled`、`rejected` 三种状态，状态一旦改变就不可逆。`async/await` 是基于 Promise 的语法糖，让异步代码写起来像同步流程；`await` 后续代码会进入微任务队列。

## Promise 核心要点

- 状态只能从 `pending` 变为 `fulfilled` 或 `rejected`。
- `then/catch/finally` 都会返回新的 Promise。
- Promise 回调是微任务。
- 错误会沿 Promise 链传递，直到被 `catch` 捕获。

## async/await 核心要点

```js
async function fn() {
  const result = await Promise.resolve(1)
  return result + 1
}

fn().then(console.log) // 2
```

- `async` 函数一定返回 Promise。
- `await` 会等待右侧 Promise settled。
- `await` 后面的代码相当于 Promise then 中的微任务。
- `try/catch` 可以捕获 await 的 rejected。

## 面试官可能追问

### Promise.then 为什么返回新的 Promise？

这样才能实现链式调用，并允许每一步对值或错误进行转换。

### async/await 如何捕获错误？

```js
try {
  await request()
} catch (e) {
  console.error(e)
}
```

### Promise.all 和 Promise.allSettled 区别？

- `Promise.all` 任一 reject 就整体 reject。
- `Promise.allSettled` 会等待全部完成，并返回每个任务的状态。

## 常见错误回答

- 认为 `await` 会阻塞整个 JS 线程。
- 不知道 async 函数返回 Promise。
- 不知道 Promise 回调属于微任务。

## 相关页面

- [[30_知识库_Wiki/概念/JavaScript/事件循环与宏任务微任务]]
- [[30_知识库_Wiki/概念/JavaScript/手写简易版Promise]]
- [[30_知识库_Wiki/概念/JavaScript/手写Promise.all]]
- [[30_知识库_Wiki/概念/Promise 并发控制]]
- [[30_知识库_Wiki/概念/Fetch 超时与重试]]
