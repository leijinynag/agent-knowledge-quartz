---
type: wiki
status: generated
created: 2026-06-26
updated: 2026-06-26
aliases:
  - Foo.getName 题
  - new Foo.getName
  - JavaScript 原型经典题
tags:
  - frontend
  - javascript
  - interview
  - prototype
generated_from:
  - "[[10_原始资料_Raw/2026-06-26_本地文件_手撕代码]]"
confidence: medium
---

# JavaScript 运算符优先级与原型题

## 一句话定义

这是以 `Foo.getName` 为代表的 JavaScript 经典面试题，用来考察声明提升、函数表达式覆盖、全局变量赋值、静态方法、原型方法和 `new` 运算符优先级。

## 核心要点

- 函数声明会提升，但同名 `var` 赋值会在运行时覆盖函数声明。
- `Foo.getName` 是构造函数对象本身的静态方法。
- `Foo.prototype.getName` 是实例通过原型链访问的方法。
- `Foo()` 普通调用时，内部未声明的 `getName = ...` 会改写外层/全局 `getName`。
- `new Foo.getName()` 与 `new Foo().getName()` 的解析方式不同。

## 原始题目输出

原始 `test.ts` 中标注的输出顺序：

```js
Foo.getName();              // 2
getName();                  // 4
Foo().getName();            // 1
getName();                  // 1
new Foo.getName();          // 2
new Foo().getName();        // 3
new new Foo().getName();    // 3
```

## 解释框架

1. 初始化阶段：函数声明 `function getName(){ console.log(5) }` 被提升。
2. 执行到 `var getName = function(){ console.log(4) }` 后，变量值变成输出 4 的函数。
3. `Foo.getName()` 调用静态方法，输出 2。
4. `Foo()` 普通调用返回 `this`，并把 `getName` 改成输出 1 的函数。
5. `new Foo().getName()` 创建实例，实例沿原型链找到 `Foo.prototype.getName`，输出 3。

## 边界情况

- 在严格模式下，`Foo()` 中未声明的 `getName = ...` 可能抛错。
- TypeScript 严格检查下，这段代码不应作为规范 TS 工具函数看待，而是 JS 行为题。
- `new` 与成员访问的优先级是本题关键。

## 面试讲解要点

- 先画出 `Foo`、`Foo.prototype`、实例对象、全局 `getName` 的关系。
- 再按代码执行顺序解释每一行输出。
- 不要把 `Foo.getName` 和 `Foo.prototype.getName` 混淆。

## 与其他概念的关系

- 相关：[[30_知识库_Wiki/概念/手写 new]]、[[30_知识库_Wiki/概念/手写 instanceof]]
- 上位主题：[[30_知识库_Wiki/主题/前端手写代码]]

## 来源

- [[10_原始资料_Raw/2026-06-26_本地文件_手撕代码]]

## 待澄清

- 是否需要补充运算符优先级表和逐行 AST/解析过程？
