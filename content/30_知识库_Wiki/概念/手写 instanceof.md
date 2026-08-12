---
type: wiki
status: needs_review
created: 2026-06-26
updated: 2026-06-26
aliases:
  - myInstanceOf
  - 模拟 instanceof
tags:
  - frontend
  - javascript
  - interview
  - prototype
generated_from:
  - "[[10_原始资料_Raw/2026-06-26_本地文件_手撕代码]]"
confidence: low
---

# 手写 instanceof

## 一句话定义

手写 `instanceof` 是沿对象的原型链向上查找，判断是否能找到构造函数的 `prototype`。

## 核心要点

`L instanceof R` 的核心判断：

1. 取右侧构造函数的 `R.prototype`。
2. 从左侧对象的原型开始向上查找。
3. 如果某一层等于 `R.prototype`，返回 `true`。
4. 如果查到 `null` 仍未匹配，返回 `false`。

## 原始实现摘要

原始 `InstanceOf.js` 试图保存 `R` 的 prototype 到 `RP`，保存 `L` 的原型到 `LP`，然后循环向上查找。

## 已知问题

原始代码存在明显问题，需要复核：

```js
const RP=R.portotype
let LP=L._proto_
```

应分别是：

- `R.prototype`
- `L.__proto__`，或更推荐 `Object.getPrototypeOf(L)`

此外，原始代码在 `LP === null` 时只有 `false` 表达式，没有 `return false`，会导致无法正确终止。

## 推荐实现要点

```js
function myInstanceOf(left, right) {
  if (left == null) return false
  const target = right.prototype
  let proto = Object.getPrototypeOf(left)

  while (proto !== null) {
    if (proto === target) return true
    proto = Object.getPrototypeOf(proto)
  }

  return false
}
```

## 边界情况

- 左侧是 `null` 或 `undefined` 时应返回 `false`。
- 右侧必须是可调用且有 `prototype` 的构造函数。
- `Symbol.hasInstance` 可以自定义真实 `instanceof` 的行为，简单手写版通常不覆盖。
- 跨 iframe 场景中，内建对象的构造函数引用可能不同。

## 面试讲解要点

- 讲清楚“查的是原型链，不是构造函数本身”。
- 推荐使用 `Object.getPrototypeOf`，避免依赖非标准历史属性 `__proto__`。

## 与其他概念的关系

- 相关：[[30_知识库_Wiki/概念/手写 new]]、[[30_知识库_Wiki/概念/JavaScript 运算符优先级与原型题]]
- 上位主题：[[30_知识库_Wiki/主题/前端手写代码]]

## 来源

- [[10_原始资料_Raw/2026-06-26_本地文件_手撕代码]]

## 待澄清

- 是否需要补充支持 `Symbol.hasInstance` 的更完整版本？
