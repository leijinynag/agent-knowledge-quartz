---
type: wiki
status: draft
created: 2026-07-11
updated: 2026-07-11
aliases:
  - JS继承方式
  - JavaScript继承
  - 继承方式总结
tags:
  - frontend/javascript
  - interview/frontend
generated_from: []
confidence: medium
---

# JavaScript继承方式

## 30秒回答

JavaScript 继承本质上是围绕“复用构造函数属性”和“复用原型方法”展开的。常见方式包括原型链继承、构造函数继承、组合继承、原型式继承、寄生式继承、寄生组合继承和 ES6 class 继承。面试中重点推荐 **寄生组合继承** 和 **ES6 class extends**，因为它们既能继承实例属性，又能复用原型方法，并避免重复调用父构造函数。

---

## 总览表

| 方式 | 核心思路 | 优点 | 缺点 | 面试评价 |
|---|---|---|---|---|
| 原型链继承 | 子构造函数的 prototype 指向父类实例 | 能复用父类原型方法 | 引用类型属性共享；不能向父构造函数传参 | 基础概念，缺陷明显 |
| 构造函数继承 | 在子构造函数中 `Parent.call(this)` | 可传参；实例属性独立 | 无法复用父类原型方法 | 解决属性问题，但方法复用差 |
| 组合继承 | 构造函数继承 + 原型链继承 | 属性独立；方法复用 | 父构造函数调用两次 | 比较常用，但不完美 |
| 原型式继承 | 基于已有对象创建新对象 | 简单，类似 `Object.create` | 引用属性共享；没有构造过程 | 适合对象级复用 |
| 寄生式继承 | 原型式继承后增强对象 | 灵活，可增强实例 | 方法难复用，每次创建新函数 | 不常单独使用 |
| 寄生组合继承 | `Parent.call(this)` + `Object.create(Parent.prototype)` | 属性独立；方法复用；父构造函数只调用一次 | 写法稍复杂 | ES5 中较理想方案 |
| ES6 class 继承 | `class Child extends Parent` | 语法清晰；接近面向对象写法 | 本质仍是原型继承；需要理解 super | 现代项目推荐写法 |

---

## 1. 原型链继承

### 核心思路

让子类的原型对象指向父类实例：

```js
function Parent() {
  this.colors = ['red', 'blue']
}

Parent.prototype.sayName = function () {
  console.log('parent')
}

function Child() {}

Child.prototype = new Parent()
Child.prototype.constructor = Child

const c1 = new Child()
const c2 = new Child()

c1.colors.push('green')
console.log(c2.colors) // ['red', 'blue', 'green']
```

### 优点

- 实现简单。
- 子实例可以访问父类原型方法。
- 能体现原型链继承的本质。

### 缺点

- 父类实例上的引用类型属性会被所有子实例共享。
- 创建子实例时不能向父构造函数传参。
- 子类原型被父类实例替换后，需要修正 `constructor`。

### 面试表达

> 原型链继承能复用父类原型方法，但最大问题是引用类型属性共享。如果父构造函数里有数组或对象属性，所有子实例会共享同一份引用，所以它一般只作为理解原型链的基础方案。

---

## 2. 构造函数继承

### 核心思路

在子构造函数内部调用父构造函数，并绑定 `this`：

```js
function Parent(name) {
  this.name = name
  this.colors = ['red', 'blue']
}

Parent.prototype.sayName = function () {
  console.log(this.name)
}

function Child(name) {
  Parent.call(this, name)
}

const c1 = new Child('a')
const c2 = new Child('b')

c1.colors.push('green')
console.log(c2.colors) // ['red', 'blue']
```

### 优点

- 可以向父构造函数传参。
- 每个子实例都有独立的实例属性。
- 解决了原型链继承中引用属性共享的问题。

### 缺点

- 无法继承父类原型上的方法。
- 如果方法写在构造函数内部，每次创建实例都会重新创建函数，浪费内存。

### 面试表达

> 构造函数继承解决了实例属性独立和传参问题，但它只借用了父构造函数，没有建立父子原型链，所以父类原型方法无法被复用。

---

## 3. 组合继承

### 核心思路

同时使用构造函数继承和原型链继承：

```js
function Parent(name) {
  this.name = name
  this.colors = ['red', 'blue']
}

Parent.prototype.sayName = function () {
  console.log(this.name)
}

function Child(name, age) {
  Parent.call(this, name) // 第二次调用 Parent
  this.age = age
}

Child.prototype = new Parent() // 第一次调用 Parent
Child.prototype.constructor = Child

Child.prototype.sayAge = function () {
  console.log(this.age)
}
```

### 优点

- 可以向父构造函数传参。
- 实例属性独立。
- 可以复用父类原型方法。
- 是比较经典、完整的继承方案。

### 缺点

- 父构造函数会被调用两次：
  - 一次用于创建子类原型。
  - 一次用于创建子类实例。
- 子类原型上会多出一份不必要的父类实例属性。

### 面试表达

> 组合继承把构造函数继承和原型链继承结合起来，解决了属性独立和方法复用问题。但它的问题是会调用两次父构造函数，所以还不是最优方案。

---

## 4. 原型式继承

### 核心思路

基于一个已有对象创建新对象，本质类似 `Object.create()`：

```js
const parent = {
  name: 'parent',
  colors: ['red', 'blue']
}

const child = Object.create(parent)
child.name = 'child'

child.colors.push('green')
```

手写形式：

```js
function object(o) {
  function F() {}
  F.prototype = o
  return new F()
}
```

### 优点

- 写法简单。
- 不需要定义构造函数。
- 适合在已有对象基础上创建新对象。

### 缺点

- 引用类型属性仍然会共享。
- 没有构造函数过程，不适合需要初始化参数的类式场景。

### 面试表达

> 原型式继承更像对象级别的复用，而不是构造函数级别的继承。它适合基于已有对象创建相似对象，但引用属性共享问题仍然存在。

---

## 5. 寄生式继承

### 核心思路

在原型式继承基础上，对新对象进行增强：

```js
function createAnother(original) {
  const clone = Object.create(original)
  clone.sayHi = function () {
    console.log('hi')
  }
  return clone
}

const child = createAnother({ name: 'parent' })
```

### 优点

- 灵活，可以在创建对象后增强能力。
- 适合对已有对象做包装和扩展。

### 缺点

- 每次创建对象都会创建新的方法，方法无法复用。
- 仍然不是理想的类式继承方案。

### 面试表达

> 寄生式继承是在原型式继承上增强对象，灵活但方法复用差。它通常不是单独推荐的继承方式，而是理解寄生组合继承的铺垫。

---

## 6. 寄生组合继承

### 核心思路

使用 `Parent.call(this)` 继承实例属性，用 `Object.create(Parent.prototype)` 继承原型方法，避免调用两次父构造函数。

```js
function Parent(name) {
  this.name = name
  this.colors = ['red', 'blue']
}

Parent.prototype.sayName = function () {
  console.log(this.name)
}

function Child(name, age) {
  Parent.call(this, name)
  this.age = age
}

Child.prototype = Object.create(Parent.prototype)
Child.prototype.constructor = Child

Child.prototype.sayAge = function () {
  console.log(this.age)
}

const child = new Child('Tom', 18)
child.sayName()
child.sayAge()
```

### 优点

- 可以向父构造函数传参。
- 子实例属性独立。
- 可以复用父类原型方法。
- 父构造函数只调用一次。
- 避免子类原型上出现多余父类实例属性。

### 缺点

- 写法比前几种复杂。
- 需要理解 `Object.create` 和原型链关系。

### 面试表达

> 寄生组合继承是 ES5 中比较理想的继承方案。它用 `Parent.call(this)` 初始化实例属性，用 `Object.create(Parent.prototype)` 连接原型链，既避免引用属性共享，也避免重复调用父构造函数。

---

## 7. ES6 class 继承

### 核心思路

使用 `class` 和 `extends`：

```js
class Parent {
  constructor(name) {
    this.name = name
    this.colors = ['red', 'blue']
  }

  sayName() {
    console.log(this.name)
  }
}

class Child extends Parent {
  constructor(name, age) {
    super(name)
    this.age = age
  }

  sayAge() {
    console.log(this.age)
  }
}
```

### 优点

- 语法清晰，更接近传统面向对象写法。
- 支持 `extends`、`super`、静态方法、私有字段等现代语法。
- 更适合现代工程项目。

### 缺点

- 本质仍然是原型继承，不能只停留在语法层面。
- 子类构造函数中必须先调用 `super()` 才能使用 `this`。
- 对初学者容易掩盖底层原型机制。

### 面试表达

> ES6 class 是语法糖，底层仍然依赖原型链。实际项目中我会优先使用 class 或组合模式，但面试中需要能解释它和寄生组合继承之间的关系。

---

## ES6 class 和寄生组合继承的关系

可以把 `class extends` 理解为语言层面对寄生组合继承模式的封装：

- `super()` 类似在子类构造函数中调用父构造函数。
- 子类原型会继承父类原型。
- 静态属性/方法也有对应继承关系。

但 `class` 还有更严格的语义，例如：

- class 声明不会像函数声明一样可提前调用。
- class 内部默认严格模式。
- class 方法不可枚举。
- 子类必须先 `super()` 才能访问 `this`。

---

## 面试常见追问

### 1. 为什么组合继承会调用两次父构造函数？

因为：

```js
Child.prototype = new Parent()
```

这里调用了一次；创建子实例时：

```js
Parent.call(this)
```

又调用了一次。

寄生组合继承用 `Object.create(Parent.prototype)` 替代 `new Parent()`，因此只调用一次父构造函数。

### 2. 为什么要修正 constructor？

当执行：

```js
Child.prototype = Object.create(Parent.prototype)
```

新的 `Child.prototype` 对象默认没有正确的 `constructor`，会沿原型链找到 `Parent.prototype.constructor`。为了语义正确，需要手动设置：

```js
Child.prototype.constructor = Child
```

### 3. 继承和组合哪个更推荐？

现代前端中，很多场景更推荐组合而不是深层继承。继承适合明确的 is-a 关系；组合更灵活，适合复用行为，减少层级耦合。React 中组件复用也更倾向组合。

---

## 记忆口诀

```text
原型链：方法能继承，属性会共享。
构造函数：属性能独立，方法不能复用。
组合继承：属性 + 方法都行，但父构造调两次。
寄生组合：属性 + 方法都行，父构造只调一次。
class：现代语法，本质还是原型链。
```

## 相关页面

- [[30_知识库_Wiki/概念/JavaScript/原型链与继承]]
- [[30_知识库_Wiki/概念/手写 new]]
- [[30_知识库_Wiki/概念/手写 instanceof]]
- [[30_知识库_Wiki/主题/前端面试/JavaScript面试]]

## 待澄清

- 是否需要继续补充“手写 `extends` / `inheritPrototype`”题目？
- 是否需要补充 class 静态属性继承和 `super` 细节？
