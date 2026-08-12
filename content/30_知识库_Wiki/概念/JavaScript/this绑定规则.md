---
type: wiki
status: draft
created: 2026-06-26
updated: 2026-06-26
aliases:
  - this绑定
  - this 指向
tags:
  - frontend/javascript
  - interview/frontend
generated_from: []
confidence: medium
---

# this绑定规则

## 30秒回答

`this` 的值取决于函数的调用方式，而不是函数定义位置。常见规则包括默认绑定、隐式绑定、显式绑定、new 绑定和箭头函数词法绑定。优先级大致是：`new` 绑定 > 显式绑定 > 隐式绑定 > 默认绑定；箭头函数没有自己的 `this`，会捕获外层词法作用域的 `this`。

## 核心规则

### 默认绑定

```js
function fn() { console.log(this) }
fn() // 非严格模式 window/global；严格模式 undefined
```

### 隐式绑定

```js
const obj = { name: 'a', fn() { console.log(this.name) } }
obj.fn() // 'a'
```

### 显式绑定

```js
fn.call(obj)
fn.apply(obj)
const bound = fn.bind(obj)
```

### new 绑定

```js
function Foo() { this.x = 1 }
const f = new Foo()
```

### 箭头函数

```js
const obj = {
  name: 'a',
  fn: () => console.log(this.name)
}
```

箭头函数不会因为 `obj.fn()` 改变 `this`，它捕获定义时外层作用域的 `this`。

## 面试官可能追问

### 箭头函数能不能作为构造函数？

不能。箭头函数没有自己的 `this`，也没有 `prototype`，不能被 `new` 调用。

### bind 后还能被 call 改变 this 吗？

不能。`bind` 返回的新函数的 `this` 已经绑定，后续 `call/apply` 不能再改变它的绑定对象。

### setTimeout 中 this 指向谁？

取决于传入的函数和运行环境。普通函数通常丢失原对象绑定；箭头函数可以捕获外层 `this`。

## 常见错误回答

- 说 `this` 永远指向调用者，但忽略默认绑定和箭头函数。
- 忽略严格模式。
- 不知道 `bind` 和 `call/apply` 的区别。

## 相关页面

- [[30_知识库_Wiki/概念/JavaScript/闭包]]
- [[30_知识库_Wiki/概念/手写 new]]
