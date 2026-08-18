---
type: wiki
status: generated
created: 2026-06-26
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

## 内部协议

Adapter 至少要统一三类对象：

```ts
type ModelRequest = {
  model: string
  messages: Message[]
  tools?: ToolDefinition[]
  responseSchema?: unknown
  timeoutMs: number
}

type ModelEvent =
  | { type: "text_delta"; text: string }
  | { type: "tool_call"; id: string; name: string; arguments: unknown }
  | { type: "usage"; inputTokens: number; outputTokens: number }
  | { type: "completed"; finishReason: string }

type ModelError = {
  code: "rate_limited" | "timeout" | "invalid_request" | "provider_error"
  retryable: boolean
  providerRequestId?: string
}
```

内部协议应只保留 Runtime 真正需要的语义。供应商特有字段可以放在 `metadata` 中，避免污染核心状态机。

## 能力矩阵

不同模型可能在上下文长度、Tool Use、结构化输出、视觉输入和缓存方面存在差异。Adapter 层应暴露能力查询，让模型路由在请求发出前判断兼容性，而不是等 Provider 报错。

## 流式归一化

- 工具参数可能分多个增量到达，需要按调用 ID 聚合。
- 文本、思考、引用和工具事件不能混成一个字符串。
- 中断时保留已接收事件和 Provider 请求 ID。
- 完成原因要映射为统一枚举。
- usage 可能只在结束事件出现，不能提前假设。

## 错误与重试

Adapter 负责把 Provider 错误转成统一错误类型，但重试策略应由上层 Model Gateway 控制。否则每个 Adapter 各自重试，可能突破整个任务的时间和费用预算。

## 测试策略

- 契约测试：相同输入在不同 Adapter 上生成合法内部事件。
- 流式测试：参数增量、半包、中断和取消。
- 错误映射测试：限流、超时、非法参数和服务故障。
- Golden trace：固定原始响应，验证归一化结果。
- Mock Provider：稳定复现 Runtime 的边界场景。

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
- [[30_知识库_Wiki/概念/Agent开发/Agent模型路由与降级]]
- [[30_知识库_Wiki/概念/Agent开发/Structured Outputs结构化输出]]
