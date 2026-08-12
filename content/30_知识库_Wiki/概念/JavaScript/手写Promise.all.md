---
type: wiki
status: generated
created: 2026-07-11
updated: 2026-07-11
aliases:
  - Promise.all
  - 手写Promise.all
  - myPromiseAll
tags:
  - frontend/javascript
  - interview/coding
  - promise
generated_from: []
confidence: medium
---

# 手写Promise.all

## 题目描述

实现 `Promise.all(iterable)`：

- 接收一个可迭代对象，通常是 Promise 数组。
- 所有任务 fulfilled 后，按输入顺序返回结果数组。
- 任意一个任务 rejected，整体立即 rejected。
- 输入中可以包含普通值。
- 空数组应直接 resolve 为 `[]`。

---

## 30秒回答

`Promise.all` 的核心是计数器和结果数组。遍历输入数组，对每一项用 `Promise.resolve` 包一层，兼容普通值和 Promise。每个任务成功后把结果放到对应下标，并让完成计数加一；当完成数量等于输入长度时 resolve 结果数组。任意一个任务失败时直接 reject。

---

## 基础实现

```js
function promiseAll(promises) {
  return new Promise((resolve, reject) => {
    if (!Array.isArray(promises)) {
      return reject(new TypeError('promises must be an array'))
    }

    const result = []
    let count = 0
    const len = promises.length

    if (len === 0) {
      resolve([])
      return
    }

    promises.forEach((item, index) => {
      Promise.resolve(item).then(
        value => {
          result[index] = value
          count++

          if (count === len) {
            resolve(result)
          }
        },
        reason => {
          reject(reason)
        }
      )
    })
  })
}
```

---

## 支持 iterable 的版本

原生 `Promise.all` 接收 iterable，不只接收数组。

```js
function promiseAll(iterable) {
  return new Promise((resolve, reject) => {
    const items = Array.from(iterable)
    const result = []
    let count = 0

    if (items.length === 0) {
      resolve([])
      return
    }

    items.forEach((item, index) => {
      Promise.resolve(item).then(
        value => {
          result[index] = value
          count++
          if (count === items.length) {
            resolve(result)
          }
        },
        reject
      )
    })
  })
}
```

---

## 使用示例

```js
promiseAll([
  Promise.resolve(1),
  2,
  new Promise(resolve => setTimeout(() => resolve(3), 100))
]).then(console.log)

// [1, 2, 3]
```

失败示例：

```js
promiseAll([
  Promise.resolve(1),
  Promise.reject('error'),
  Promise.resolve(3)
]).catch(console.log)

// error
```

---

## 实现要点

### 1. 为什么要用 Promise.resolve 包一层？

因为输入项可能是普通值，也可能是 Promise 或 thenable：

```js
Promise.resolve(item).then(...)
```

这样可以统一处理。

### 2. 为什么结果要按 index 存？

Promise 完成顺序不一定等于输入顺序，但 `Promise.all` 要按输入顺序返回结果。

```js
result[index] = value
```

### 3. 为什么要处理空数组？

如果不处理空数组，`count === len` 的逻辑永远不会触发，Promise 会一直 pending。

```js
if (items.length === 0) resolve([])
```

### 4. 为什么任意 reject 直接 reject？

这是 `Promise.all` 的语义：只要有一个失败，整体失败，并使用第一个失败原因。

---

## 边界情况

- 输入为空：resolve `[]`。
- 输入包含普通值：当作已 fulfilled。
- 输入包含 thenable：由 `Promise.resolve` 展开。
- 多个任务 reject：第一个 reject 决定整体结果。
- 完成顺序与返回顺序不同：必须按输入顺序返回。
- iterable 转数组时可能抛错：更完整实现需要 try/catch。

---

## 更严谨一点：捕获 Array.from 错误

```js
function promiseAll(iterable) {
  return new Promise((resolve, reject) => {
    let items

    try {
      items = Array.from(iterable)
    } catch (err) {
      reject(err)
      return
    }

    const result = []
    let count = 0

    if (items.length === 0) {
      resolve([])
      return
    }

    items.forEach((item, index) => {
      Promise.resolve(item).then(
        value => {
          result[index] = value
          count++
          if (count === items.length) resolve(result)
        },
        reject
      )
    })
  })
}
```

---

## Promise.all 和 Promise.allSettled 对比

| API | 成功条件 | 失败行为 | 返回值 |
|---|---|---|---|
| `Promise.all` | 全部 fulfilled | 任一 rejected 就整体 rejected | fulfilled 值数组 |
| `Promise.allSettled` | 等全部 settled | 不会因为单个失败整体 reject | 每项 `{ status, value/reason }` |
| `Promise.race` | 第一个 settled | 第一个是 reject 就 reject | 第一个 settled 的结果 |
| `Promise.any` | 任一 fulfilled | 全部 rejected 才 reject | 第一个 fulfilled 的值 |

---

## 面试官可能追问

### Promise.all 是并发还是串行？

`Promise.all` 本身不会控制并发。传入的 Promise 通常已经开始执行，它只是等待这些 Promise 的结果。如果要限制并发，需要实现任务队列，见 [[30_知识库_Wiki/概念/Promise 并发控制]]。

### 如果想失败也拿到所有结果怎么办？

用 `Promise.allSettled`，或把每个 Promise 自己 catch 成 fulfilled：

```js
Promise.all(tasks.map(p => p.catch(err => err)))
```

### Promise.all 如何保持顺序？

每个任务完成后把结果写入对应输入下标：`result[index] = value`，而不是 `push`。

---

## 面试表达模板

> 手写 Promise.all 的核心是结果数组和完成计数。遍历输入时用 Promise.resolve 统一包装普通值和 Promise，每个任务完成后按原下标写入 result，并让计数加一；当计数等于任务数量时 resolve。只要任意任务 reject，就立刻 reject。注意空数组要直接 resolve []，否则会一直 pending。

## 相关页面

- [[30_知识库_Wiki/概念/JavaScript/Promise与async-await]]
- [[30_知识库_Wiki/概念/JavaScript/手写简易版Promise]]
- [[30_知识库_Wiki/概念/Promise 并发控制]]
- [[30_知识库_Wiki/主题/前端面试/手撕代码与算法]]
