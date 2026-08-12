---
type: wiki
status: generated
created: 2026-06-26
updated: 2026-06-26
aliases:
  - Provider Adapter
  - 模型适配层
tags:
  - agent-development
  - llm-app
confidence: medium
---

# Provider Adapter模型抽象

## 一句话定义

Provider Adapter 是把不同模型服务的输入输出格式转换为统一内部协议的适配层，让 Agent Runtime 不依赖某一个具体模型 API。

## 核心要点

- 不同 Provider 的 streaming、tool call、usage、reasoning/thinking 事件格式不同。
- Runtime 应该消费统一 ModelEvent，而不是直接消费各 Provider 原始事件。
- Mock Provider 可以用于测试 Runtime，不依赖真实模型调用。

## 在 Velaire 中的体现

Velaire 支持 Anthropic、OpenAI-compatible 和 Mock Provider。各 provider 把外部 API 事件转换成内部标准事件，再交给 Runtime 处理。

相关项目：[[30_知识库_Wiki/项目/个人项目/Velaire]]

## 面试表达

> Provider Adapter 的价值是隔离变化。模型 API 的 tool call 和 streaming 细节可能不同，但 Runtime 的状态机应该稳定，所以我把 provider-specific 逻辑放在 adapter 层。

## 高频追问

- 如何统一不同模型的 tool call 格式？
- streaming 中断如何处理？
- usage/token 信息怎么归一化？
- Mock Provider 在测试中有什么价值？

## 相关页面

- [[30_知识库_Wiki/主题/Agent开发/LLM应用开发]]
- [[30_知识库_Wiki/主题/Agent开发/Agent系统设计]]
