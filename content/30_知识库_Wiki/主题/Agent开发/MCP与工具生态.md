---
type: wiki
status: draft
created: 2026-06-26
updated: 2026-06-26
aliases:
  - MCP
  - Model Context Protocol
  - 工具生态
tags:
  - interview/agent
  - mcp
confidence: medium
---

# MCP与工具生态

## 一句话定义

MCP 是一种把外部系统能力标准化暴露给模型/Agent 的协议，让工具接入从“一次性定制”变成“可复用生态”。

## 重点范围

- MCP server / client / tool 的关系
- MCP 与普通 function calling 的区别
- 第三方系统集成
- 凭证与权限
- 工具发现与工具选择
- 工具输出过大时的处理

## 面试表达要点

MCP 解决的是工具集成和生态复用问题，不是模型推理本身。它适合把 GitHub、Linear、Notion、数据库、内部系统等能力以统一方式接入 Agent。

## 高频问题占位

- [ ] MCP 和 function calling 有什么区别？
- [ ] MCP Server 应该如何设计？
- [ ] 工具权限和认证如何处理？
- [ ] 工具太多时如何避免模型混乱？
