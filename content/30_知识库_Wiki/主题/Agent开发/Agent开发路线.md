---
type: wiki
status: draft
created: 2026-06-26
updated: 2026-06-26
aliases:
  - Agent开发学习路线
  - Agent Interview Roadmap
tags:
  - interview/agent
  - agent-development
confidence: medium
---

# Agent开发路线

## 学习顺序

1. LLM 应用基础：prompt、messages、streaming、structured outputs。
2. Tool Use：工具定义、工具调用循环、错误处理。
3. Workflow vs Agent：什么时候写固定流程，什么时候让模型自主决策。
4. MCP：把外部工具标准化接入 Agent。
5. Memory / Context：长期记忆、上下文压缩、缓存。
6. Evaluation：如何判断 Agent 是否完成任务。
7. Safety：权限、确认、secret 管理、prompt injection。

## 主题入口

- [[30_知识库_Wiki/主题/Agent开发/LLM应用开发]]
- [[30_知识库_Wiki/主题/Agent开发/Agent系统设计]]
- [[30_知识库_Wiki/主题/Agent开发/Tool Use与函数调用]]
- [[30_知识库_Wiki/主题/Agent开发/MCP与工具生态]]
- [[30_知识库_Wiki/主题/Agent开发/Agent记忆与上下文管理]]
- [[30_知识库_Wiki/主题/Agent开发/Agent评估与可观测性]]
- [[30_知识库_Wiki/主题/Agent开发/Agent安全与权限]]

## 面试目标

不仅要会调 API，还要能讲清楚：架构分层、工具边界、权限控制、上下文管理、错误恢复和评估闭环。
