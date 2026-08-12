---
type: wiki
status: generated
created: 2026-06-26
updated: 2026-06-26
aliases:
  - once
  - 单次执行函数
tags:
  - frontend
  - javascript
  - interview
  - function
generated_from:
  - "[[10_原始资料_Raw/2026-06-26_本地文件_手撕代码]]"
confidence: medium
---

# once 函数

## 一句话定义

`once` 是一种函数包装器，确保目标函数只在第一次调用时执行，之后调用直接返回第一次执行的结果。

## 核心要点

- 用闭包保存 `called` 状态。
- 用闭包保存第一次执行的返回值。
- 第一次调用时用 `fn.apply(this, args)` 保留调用上下文。
- 后续调用不再执行原函数，只返回缓存值。

## 原始实现摘要

原始实现维护 `called=false` 与 `value`。第一次调用时执行 `fn` 并缓存返回值；之后无论传入什么参数，都返回首次缓存值。

## 推荐实现要点

```js
function once(fn) {
  let called = false
  let value
  return function (...args) {
    if (!called) {
      called = true
      value = fn.apply(this, args)
    }
    return value
  }
}
```

## 边界情况

- 如果第一次执行抛错，是否应标记为已调用？当前实现会在 `fn` 抛错前已经设置 `called=true`。
- 如果 `fn` 返回 Promise，后续调用会返回同一个 Promise。
- 如果需要可重置版本，可以暴露 `reset()`，但这不再是纯粹 once。

## 面试讲解要点

- 本质是闭包状态机。
- `called` 防止副作用重复发生。
- 常见场景：初始化、埋点、只允许提交一次的操作。

## 与其他概念的关系

- 相关：[[30_知识库_Wiki/概念/函数柯里化]]、[[30_知识库_Wiki/概念/防抖与节流]]
- 上位主题：[[30_知识库_Wiki/主题/前端手写代码]]

## 来源

- [[10_原始资料_Raw/2026-06-26_本地文件_手撕代码]]

## 待澄清

- 是否希望 once 在第一次执行抛错时允许下次重试？
