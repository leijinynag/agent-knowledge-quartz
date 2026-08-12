---
type: wiki
status: draft
created: 2026-06-26
updated: 2026-06-26
aliases:
  - WebSocket在线状态
  - Socket.io实时通信
  - 实时通信
tags:
  - frontend
  - network
  - websocket
  - interview
generated_from:
  - "用户提供的简历截图"
confidence: medium
---

# WebSocket在线状态与实时通信

## 一句话定义

WebSocket 是一种在客户端和服务端之间建立持久双向连接的通信方式，适合在线状态、即时消息、协同编辑、实时通知等场景。

## 简历关联

简历中的在线教学管理系统提到：使用 Socket.io Client 实现前端实时通信架构，支持消息推送、在线状态同步、断线重连、心跳检测机制。

## 设计要点

- 连接管理：初始化、重连、断开、销毁。
- 心跳机制：检测连接是否存活。
- 用户状态：online / offline / away。
- 消息确认：ack、重试、去重。
- 权限认证：连接时带 token，并处理过期刷新。
- UI 状态：连接中、重连中、离线、已连接。

## 和 SSE 的区别

| 维度 | WebSocket | SSE |
|---|---|---|
| 方向 | 双向 | 服务端到客户端单向 |
| 适合场景 | 聊天、协作、在线状态 | 模型流式输出、通知、日志流、任务进度 |
| 协议复杂度 | 更高，需要心跳、重连、房间/订阅等设计 | 更简单，基于 HTTP，浏览器原生自动重连 |
| 请求方式 | 建立独立 WebSocket 连接 | 普通 HTTP 长连接 |
| 前端原生支持 | `WebSocket` | `EventSource` |
| 自定义请求头 | 建连时能力有限，通常用协议/查询/cookie | 原生 EventSource 不方便带自定义 header |

更完整的 SSE 总结见：[[30_知识库_Wiki/概念/Agent开发/SSE流式Agent状态同步]]。

## 面试回答模板

> 在线状态和实时消息更适合 WebSocket，因为它需要双向通信和低延迟状态同步。我会把连接管理、心跳、断线重连、消息确认和认证续期封装成统一模块，业务组件只消费连接状态和消息事件。如果是 AI 输出、日志流或任务进度这类服务端单向推送，我会优先考虑 SSE。

## 高频追问

- WebSocket 如何鉴权？
- 断线重连如何避免消息丢失？
- 如何处理重复消息？
- WebSocket 和 SSE 如何选择？

## 相关页面

- [[30_知识库_Wiki/主题/前端面试/网络面试]]
- [[30_知识库_Wiki/概念/Agent开发/SSE流式Agent状态同步]]
