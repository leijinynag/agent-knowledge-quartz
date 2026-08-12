---
type: wiki
status: generated
created: 2026-06-26
updated: 2026-06-26
aliases:
  - myNew
  - 模拟 new
tags:
  - frontend
  - javascript
  - interview
  - prototype
generated_from:
  - "[[10_原始资料_Raw/2026-06-26_本地文件_手撕代码]]"
confidence: medium
---

# 手写 new

## 一句话定义

手写 `new` 是模拟 JavaScript `new` 运算符的对象创建过程：创建对象、连接原型、执行构造函数、处理返回值。

## 核心要点

`new Constructor(...args)` 大致做四件事：

1. 创建一个新对象。
2. 将新对象的原型指向 `Constructor.prototype`。
3. 以新对象作为 `this` 调用构造函数。
4. 如果构造函数返回对象或函数，则返回该值；否则返回新对象。

## 原始实现摘要

原始实现使用 `Object.create(oldObj.prototype)` 创建带有正确原型的新对象，再用 `oldObj.apply(newObj, args)` 执行构造函数，最后判断返回值是否为对象或函数。

## 推荐实现要点

```js
function myNew(Constructor, ...args) {
  const obj = Object.create(Constructor.prototype)
  const ret = Constructor.apply(obj, args)
  return ret !== null && (typeof ret === 'object' || typeof ret === 'function')
    ? ret
    : obj
}
```

## 边界情况

- 构造函数显式返回普通对象时，`new` 返回该对象。
- 构造函数返回原始值时，`new` 忽略该原始值。
- 箭头函数不能作为构造函数。
- 如果 `Constructor.prototype` 不是对象，真实 `new` 的行为更复杂。

## 面试讲解要点

- 重点不是背代码，而是说清楚原型连接和返回值规则。
- `Object.create(Constructor.prototype)` 是手写实现的关键。

## 与其他概念的关系

- 相关：[[30_知识库_Wiki/概念/手写 instanceof]]、[[30_知识库_Wiki/概念/JavaScript 运算符优先级与原型题]]
- 上位主题：[[30_知识库_Wiki/主题/前端手写代码]]

## 来源

- [[10_原始资料_Raw/2026-06-26_本地文件_手撕代码]]

## 待澄清

- 是否需要补充 `Reflect.construct` 和 ES6 class 的差异？
