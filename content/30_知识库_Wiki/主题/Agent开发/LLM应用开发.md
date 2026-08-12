---
type: wiki
status: draft
created: 2026-06-26
updated: 2026-06-26
aliases:
  - LLM App Development
  - 大模型应用开发
tags:
  - interview/agent
  - llm-app
confidence: medium
---

# LLM应用开发

## 重点范围

- Messages API / Chat API 基本形态
- System / User / Assistant messages
- Streaming
- Structured outputs
- Tool use / function calling
- File input / document input
- Token counting
- Prompt caching
- Error handling

## 重点页面

- [[30_知识库_Wiki/概念/Agent开发/SSE流式Agent状态同步]]
- [[30_知识库_Wiki/主题/Agent开发/Tool Use与函数调用]]
- [[30_知识库_Wiki/概念/Agent开发/事件驱动Agent Runtime]]
- [[30_知识库_Wiki/概念/Agent开发/Agent可观测性与RuntimeEvent]]

## 面试核心判断

不是所有 LLM 应用都需要 Agent：

- 单次分类、摘要、抽取：Single LLM Call。
- 固定多步骤流程：Workflow。
- 需要模型自主选择工具和恢复错误：Agent。

## 高频问题占位

- [x] Streaming 为什么重要？
- [x] SSE 在 LLM/Agent 产品中怎么用？
- [ ] Structured output 如何保证可解析？
- [ ] Prompt caching 适合什么场景？
- [ ] Token counting 有什么用？
- [ ] LLM API 错误如何处理和重试？
