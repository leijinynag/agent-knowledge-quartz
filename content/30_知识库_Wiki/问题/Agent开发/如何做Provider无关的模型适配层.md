---
type: wiki
status: generated
created: 2026-06-26
aliases:
  - Provider无关模型适配层设计题
  - Provider Adapter设计题
tags:
  - interview/question
  - agent-development
  - llm-app
confidence: medium
---

# 问题：如何做Provider无关的模型适配层？

## 30秒回答

我会在 Runtime 和具体模型 API 之间加一层 Provider Adapter。Runtime 只消费统一的 ModelEvent，比如 text delta、tool call、usage、done、error；不同 Provider 的 streaming、tool call、thinking/reasoning 等差异都在 Adapter 层转换，这样 Runtime 不需要感知具体 API。

## 系统化回答

Provider Adapter 需要处理：

1. 请求格式转换：内部 message/tool/schema → provider request。
2. 流式事件转换：provider stream → 标准 ModelEvent。
3. 工具调用归一：不同 provider 的 tool call 格式统一成内部 ToolCall。
4. usage 归一：token、cost、model、finish reason。
5. 错误归一：网络错误、限流、模型拒绝、上下文超限。
6. 测试替身：Mock Provider 用于 Runtime 单测。

## 关键 trade-off

- 统一抽象可以降低 Runtime 耦合。
- 但过度抽象可能抹平 Provider 独有能力。
- 解决方式是：核心事件统一，Provider-specific metadata 保留扩展字段。

## 项目例子

- [[30_知识库_Wiki/项目/个人项目/Velaire]]
- [[30_知识库_Wiki/概念/Agent开发/Provider Adapter模型抽象]]

## 高频追问

- 不同 Provider 的 tool call 格式不一致怎么办？
- Provider 独有能力是否要暴露？
- Mock Provider 如何设计？
- streaming 中断时 Runtime 如何恢复？

## 相关页面

- [[30_知识库_Wiki/主题/Agent开发/LLM应用开发]]
- [[30_知识库_Wiki/主题/Agent开发/Agent系统设计]]
