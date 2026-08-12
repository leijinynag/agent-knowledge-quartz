---
type: wiki
status: draft
created: 2026-06-26
updated: 2026-06-26
aliases:
  - Map WeakMap
  - WeakMap
tags:
  - frontend/javascript
  - interview/frontend
generated_from: []
confidence: medium
---

# Map与WeakMap

## 30秒回答

`Map` 是键值对集合，键可以是任意类型，并且可遍历；`WeakMap` 的 key 只能是对象，而且是弱引用，不可遍历。当 WeakMap 的 key 对象没有其他强引用时，可以被垃圾回收，所以它适合存储对象关联的私有数据或缓存，比如深拷贝中记录已克隆对象。

## Map 特点

```js
const map = new Map()
map.set('a', 1)
map.set({}, 2)
map.get('a') // 1
```

- key 可以是任意类型。
- 保持插入顺序。
- 可遍历。
- 有 `size`。

## WeakMap 特点

```js
const wm = new WeakMap()
const obj = {}
wm.set(obj, 'meta')
wm.get(obj) // 'meta'
```

- key 必须是对象。
- key 是弱引用。
- 不可遍历。
- 没有 `size`。

## 面试官可能追问

### 为什么 WeakMap 不可遍历？

因为 WeakMap 的 key 是弱引用，key 是否被回收由 GC 决定。如果允许遍历，就会暴露 GC 行为并影响语义。

### WeakMap 有什么应用？

- 深拷贝处理循环引用：[[30_知识库_Wiki/概念/深拷贝]]。
- 存储对象私有数据。
- 对 DOM 节点附加元信息，避免阻止 GC。

### Map 和 Object 做字典有什么区别？

Map 的 key 可以是任意类型，且没有原型链 key 冲突问题；Object 的 key 通常会转成字符串或 Symbol。

## 常见错误回答

- 认为 WeakMap 是“性能更好的 Map”。
- 不知道 WeakMap key 必须是对象。
- 不知道 WeakMap 不可遍历的原因。

## 相关页面

- [[30_知识库_Wiki/概念/JavaScript/垃圾回收与内存泄漏]]
- [[30_知识库_Wiki/概念/深拷贝]]
