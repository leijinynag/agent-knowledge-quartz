---
type: wiki
status: draft
created: 2026-06-26
updated: 2026-06-26
aliases:
  - iframe通信
  - postMessage
  - iframe postMessage
tags:
  - frontend
  - browser
  - interview
generated_from:
  - "用户提供的简历截图"
confidence: medium
---

# iframe与postMessage通信

## 一句话定义

`postMessage` 是浏览器提供的跨窗口安全通信机制，常用于主页面与 iframe、弹窗、跨域页面之间传递结构化消息。

## 设计要点

- 定义统一消息协议：`type`、`payload`、`requestId`、`source`。
- 校验 `origin`，不要接受任意来源消息。
- 对请求-响应场景维护 pending map。
- 支持超时、错误和重试。
- 对关键状态做持久化，避免刷新或跳转后状态丢失。

## 示例协议

```ts
type IframeMessage = {
  type: string
  requestId?: string
  payload?: unknown
  source: 'parent' | 'iframe'
}
```

## 面试回答模板

> iframe 通信的关键不是简单调用 postMessage，而是设计一套可靠协议：消息类型、请求 ID、来源校验、超时处理和状态恢复。尤其在业务系统里，要避免任意 origin 消息造成安全问题，也要处理 iframe 加载时机和页面跳转后的状态恢复。

## 高频追问

- 为什么必须校验 origin？
- 如何实现 request-response？
- iframe 尚未加载完成时消息怎么处理？
- postMessage 和微前端通信有什么关系？

## 相关页面

- [[30_知识库_Wiki/概念/前端工程化/微前端与React子应用]]
- [[30_知识库_Wiki/主题/前端面试/前端场景题]]
