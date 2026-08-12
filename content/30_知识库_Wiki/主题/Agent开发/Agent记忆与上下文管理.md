---
type: wiki
status: draft
created: 2026-06-26
updated: 2026-06-26
aliases:
  - Agent Memory
  - Context Management
  - Agent上下文管理
tags:
  - interview/agent
  - agent-memory
confidence: medium
---

# Agent记忆与上下文管理

## 重点范围

- Context window
- Token counting
- Prompt caching
- Conversation history
- Compaction
- Context editing
- Long-term memory
- 文件型 memory / memory store

## 面试表达要点

Agent 的上下文不是无限的。长期运行时需要区分：

- 当前任务上下文
- 可压缩历史
- 可丢弃工具结果
- 需要长期保存的记忆
- 可从外部资料重新检索的知识

## 高频问题占位

- [ ] 上下文窗口不够怎么办？
- [ ] Prompt caching 适合哪些场景？
- [ ] memory 和 RAG 有什么区别？
- [ ] Agent 应该记住什么，不应该记住什么？
