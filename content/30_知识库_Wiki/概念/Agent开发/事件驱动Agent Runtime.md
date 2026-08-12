---
type: wiki
status: generated
created: 2026-06-26
updated: 2026-07-15
aliases:
  - Event-driven Agent Runtime
  - RuntimeEvent
tags:
  - agent-development
  - interview/agent
confidence: medium
---

# 事件驱动Agent Runtime

## 一句话定义

事件驱动 Agent Runtime 是把 Agent 执行过程抽象为一连串可消费事件，让 CLI、TUI、Web UI、日志、测试和回放都能共享同一个执行内核。

## 核心要点

- Runtime 不直接绑定 UI，而是输出 `AsyncIterable<RuntimeEvent>`。
- 事件可以覆盖：模型开始/结束、文本增量、工具调用、工具结果、审批、错误、完成状态等。
- UI 只负责消费事件并渲染，不重新实现 Agent loop。
- 日志和回放也可以基于同一事件协议。

## 在 Velaire 中的体现

Velaire 的 CLI/TUI、React Web Workbench、JSONL run log 和测试都围绕 RuntimeEvent 工作。这样可以保证同一个 Agent 核心在不同界面下行为一致。

相关项目：[[30_知识库_Wiki/项目/个人项目/Velaire]]

## 面试表达

> 我没有让 TUI 和 Web UI 各自维护一套 Agent 状态，而是把 Runtime 抽象成事件源。所有 UI 和日志系统消费同一套 RuntimeEvent，这样更容易做调试、回放和可观测性。

## 高频追问

- 事件粒度怎么设计？
- 如何保证事件顺序？
- SSE 和本地 AsyncIterable 怎么映射？
- 工具执行失败如何通过事件表达？

## 相关页面

- [[30_知识库_Wiki/概念/Agent开发/Agent可观测性与RuntimeEvent]]
- [[30_知识库_Wiki/主题/Agent开发/Agent系统设计]]
