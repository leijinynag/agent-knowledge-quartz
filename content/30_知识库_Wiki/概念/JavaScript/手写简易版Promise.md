---
type: wiki
status: generated
created: 2026-07-11
updated: 2026-07-11
aliases:
  - MyPromise
  - 手写Promise
  - 简易Promise
tags:
  - frontend/javascript
  - interview/coding
  - promise
generated_from: []
confidence: medium
---

# 手写简易版Promise

## 题目描述

实现一个简易版 `Promise`，至少支持：

- 三种状态：`pending`、`fulfilled`、`rejected`。
- 构造函数接收 executor。
- `then(onFulfilled, onRejected)`。
- 异步 resolve/reject 后仍能触发 then。
- then 链式调用。

> 说明：完整 Promise/A+ 规范非常细，面试中通常先写简化版，再逐步补充 then 链式调用和 Promise 解析过程。

---

## 30秒回答

Promise 的核心是状态机和回调队列。初始是 `pending`，调用 `resolve` 后变成 `fulfilled`，调用 `reject` 后变成 `rejected`，状态一旦改变不可逆。`then` 根据当前状态立即执行回调，或者在 pending 时把回调存入队列，等状态改变后再统一触发。为了支持链式调用，`then` 必须返回一个新的 Promise，并根据回调返回值决定新 Promise 的状态。

---

## 第一版：支持状态和异步回调

```js
class MyPromise {
  constructor(executor) {
    this.status = 'pending'
    this.value = undefined
    this.reason = undefined
    this.onFulfilledCallbacks = []
    this.onRejectedCallbacks = []

    const resolve = (value) => {
      if (this.status !== 'pending') return
      this.status = 'fulfilled'
      this.value = value
      this.onFulfilledCallbacks.forEach(fn => fn())
    }

    const reject = (reason) => {
      if (this.status !== 'pending') return
      this.status = 'rejected'
      this.reason = reason
      this.onRejectedCallbacks.forEach(fn => fn())
    }

    try {
      executor(resolve, reject)
    } catch (err) {
      reject(err)
    }
  }

  then(onFulfilled, onRejected) {
    if (this.status === 'fulfilled') {
      onFulfilled(this.value)
    }

    if (this.status === 'rejected') {
      onRejected(this.reason)
    }

    if (this.status === 'pending') {
      this.onFulfilledCallbacks.push(() => {
        onFulfilled(this.value)
      })
      this.onRejectedCallbacks.push(() => {
        onRejected(this.reason)
      })
    }
  }
}
```

### 这一版的问题

- `then` 没有返回新的 Promise，不能链式调用。
- 没有处理 `onFulfilled/onRejected` 不是函数的情况。
- 没有处理回调返回值是 Promise 的情况。
- 没有保证回调异步执行。

---

## 第二版：支持链式调用

```js
class MyPromise {
  constructor(executor) {
    this.status = 'pending'
    this.value = undefined
    this.reason = undefined
    this.onFulfilledCallbacks = []
    this.onRejectedCallbacks = []

    const resolve = (value) => {
      if (this.status !== 'pending') return
      this.status = 'fulfilled'
      this.value = value
      this.onFulfilledCallbacks.forEach(fn => fn())
    }

    const reject = (reason) => {
      if (this.status !== 'pending') return
      this.status = 'rejected'
      this.reason = reason
      this.onRejectedCallbacks.forEach(fn => fn())
    }

    try {
      executor(resolve, reject)
    } catch (err) {
      reject(err)
    }
  }

  then(onFulfilled, onRejected) {
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value
    onRejected = typeof onRejected === 'function' ? onRejected : reason => { throw reason }

    const promise2 = new MyPromise((resolve, reject) => {
      const fulfilledTask = () => {
        queueMicrotask(() => {
          try {
            const x = onFulfilled(this.value)
            resolvePromise(promise2, x, resolve, reject)
          } catch (err) {
            reject(err)
          }
        })
      }

      const rejectedTask = () => {
        queueMicrotask(() => {
          try {
            const x = onRejected(this.reason)
            resolvePromise(promise2, x, resolve, reject)
          } catch (err) {
            reject(err)
          }
        })
      }

      if (this.status === 'fulfilled') {
        fulfilledTask()
      } else if (this.status === 'rejected') {
        rejectedTask()
      } else {
        this.onFulfilledCallbacks.push(fulfilledTask)
        this.onRejectedCallbacks.push(rejectedTask)
      }
    })

    return promise2
  }
}
```

### Promise 解析过程

```js
function resolvePromise(promise2, x, resolve, reject) {
  if (promise2 === x) {
    return reject(new TypeError('Chaining cycle detected'))
  }

  if (x instanceof MyPromise) {
    x.then(resolve, reject)
    return
  }

  if (x !== null && (typeof x === 'object' || typeof x === 'function')) {
    let called = false

    try {
      const then = x.then

      if (typeof then === 'function') {
        then.call(
          x,
          y => {
            if (called) return
            called = true
            resolvePromise(promise2, y, resolve, reject)
          },
          r => {
            if (called) return
            called = true
            reject(r)
          }
        )
      } else {
        resolve(x)
      }
    } catch (err) {
      if (called) return
      called = true
      reject(err)
    }
  } else {
    resolve(x)
  }
}
```

---

## 使用示例

```js
const p = new MyPromise((resolve) => {
  setTimeout(() => resolve(1), 100)
})

p.then(value => {
  console.log(value) // 1
  return value + 1
}).then(value => {
  console.log(value) // 2
})
```

---

## 实现要点

### 1. 状态只能改变一次

```js
if (this.status !== 'pending') return
```

Promise 一旦 fulfilled 或 rejected，就不能再次改变。

### 2. pending 时要缓存回调

如果 executor 内部是异步 resolve：

```js
new MyPromise(resolve => {
  setTimeout(() => resolve(1), 100)
}).then(console.log)
```

then 调用时 Promise 还在 pending，因此需要把回调存入队列。

### 3. then 必须返回新 Promise

这是链式调用的基础：

```js
promise.then(fn1).then(fn2)
```

每次 then 返回的新 Promise 状态由 `fn1` 的返回值决定。

### 4. 回调返回 Promise 要展开

如果 `onFulfilled` 返回一个 Promise，那么 `promise2` 要采用这个 Promise 的最终状态。

### 5. then 回调要异步执行

原生 Promise 的 then 回调是微任务。简化实现中可以用 `queueMicrotask` 模拟。

相关：[[30_知识库_Wiki/概念/JavaScript/事件循环与宏任务微任务]]

---

## 边界情况

- executor 抛错时要 reject。
- resolve/reject 只能生效一次。
- then 参数不是函数时要透传。
- then 回调抛错时，新 Promise 要 reject。
- then 回调返回普通值时，新 Promise fulfilled。
- then 回调返回 Promise/thenable 时，新 Promise 跟随其状态。
- 返回自身 promise 时要拒绝，避免循环引用。

---

## 面试简化版可以怎么写

如果面试时间有限，可以先说明：

> 我先实现核心状态机和异步回调队列，再说明链式调用需要 then 返回新的 Promise。

基础版代码通常已经能覆盖多数考察点：状态不可逆、回调队列、异步 resolve。

---

## 高频追问

### 为什么 Promise 要有回调队列？

因为 then 可能在 Promise 仍处于 pending 时调用，需要先把回调保存起来，等 resolve/reject 后再执行。

### 为什么 then 要返回新的 Promise？

为了支持链式调用，并让每个 then 的返回值决定下一步状态。

### then 回调为什么是微任务？

原生 Promise 规范要求 then 回调异步执行，浏览器中通常进入微任务队列。这样可以保证同步代码先执行完。

### catch 怎么实现？

```js
MyPromise.prototype.catch = function (onRejected) {
  return this.then(null, onRejected)
}
```

### finally 怎么实现？

```js
MyPromise.prototype.finally = function (callback) {
  return this.then(
    value => MyPromise.resolve(callback()).then(() => value),
    reason => MyPromise.resolve(callback()).then(() => { throw reason })
  )
}
```

---

## 面试表达模板

> 手写 Promise 我会先从状态机讲起：Promise 有 pending、fulfilled、rejected 三种状态，状态只能改变一次。then 调用时，如果已经 fulfilled/rejected 就异步执行对应回调；如果还在 pending，就把回调存到队列里。为了支持链式调用，then 必须返回新的 Promise，并根据回调返回值处理普通值、Promise、thenable 和异常。

## 相关页面

- [[30_知识库_Wiki/概念/JavaScript/Promise与async-await]]
- [[30_知识库_Wiki/概念/JavaScript/事件循环与宏任务微任务]]
- [[30_知识库_Wiki/概念/JavaScript/手写Promise.all]]
- [[30_知识库_Wiki/主题/前端面试/手撕代码与算法]]
