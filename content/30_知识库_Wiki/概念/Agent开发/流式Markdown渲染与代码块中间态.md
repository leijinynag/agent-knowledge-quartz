---
type: wiki
status: generated
created: 2026-08-12
aliases:
  - Streaming Markdown
  - 流式Markdown
  - 代码块中间态
tags:
  - interview/frontend
  - frontend
  - streaming
  - markdown
confidence: medium
---

# 流式Markdown渲染与代码块中间态

## 问题不在 append 文本

LLM 流式输出时，前端拿到的是一段段增量文本。真正麻烦的是：这些增量可能刚好停在 Markdown 语法中间。

例如：

```text
```ts
const a =
```

这时代码块还没闭合。如果每个 token 都重新解析整篇 Markdown，性能差；如果直接当纯文本渲染，体验又很粗糙。

## 我会做的分层

```text
stream delta
  -> text buffer
  -> 节流 flush
  -> markdown segment
  -> UI render
```

网络接收频率和 React 渲染频率要分开。高频 token 可以先进入 buffer，再按 `requestAnimationFrame` 或 30 到 60ms 的节奏刷新 UI。

## 中间态策略

### 未闭合代码块

如果检测到代码围栏没闭合，可以临时补一个闭合标记给 parser，但不要改原始文本：

```text
displayText = rawText + "\n```"
```

这样用户能看到正常高亮，后续真正闭合时再回到真实内容。

### 未闭合链接或表格

链接、表格、列表这些结构在流式中间态也可能不完整。我的做法是：

- 正文区域尽量容忍不完整语法。
- 表格可以先按普通段落显示，完成后再升级成表格。
- 代码块内不要做 Markdown 二次解析。
- 最终 `completed` 后再做一次完整渲染。

## 性能注意点

- 不要每个 token 都 `setState`。
- 不要每次都全量计算代码高亮。
- 长回答可以按段落切片，稳定段落不再重复解析。
- 虚拟列表只适合消息很多的场景，不要为一条短消息过早复杂化。
- 移动端要注意自动滚动，不要用户往上看时强行拉到底。

## 滚动行为

我会加一个 `isAtBottom` 判断：

- 用户本来就在底部：新 token 到来时跟随滚动。
- 用户已经向上滚动：保持当前位置，显示一个回到底部入口。

否则长回答时用户想看前文，会被持续输出打断。

## 面试表达

流式 Markdown 的重点是处理中间态和渲染节奏。我会把网络增量先写入 buffer，再节流刷新；未闭合代码块可以临时补闭合标记用于展示，最终完成后再完整渲染。同时要避免每个 token 触发全量 Markdown 解析和代码高亮。

## 相关笔记

- [[30_知识库_Wiki/概念/Agent开发/前端流式Agent交互]]
- [[30_知识库_Wiki/概念/Agent开发/SSE流式Agent状态同步]]
- [[30_知识库_Wiki/主题/前端面试/前端性能优化]]
- [[30_知识库_Wiki/主题/前端面试/前端场景题]]

