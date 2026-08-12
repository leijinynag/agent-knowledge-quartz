---
type: wiki
status: draft
created: 2026-06-26
updated: 2026-06-26
aliases:
  - Tool Use
  - Function Calling
  - 工具调用
tags:
  - interview/agent
  - tool-use
confidence: medium
---

# Tool Use与函数调用

## 一句话定义

Tool Use 是让模型在需要外部能力时输出结构化工具调用，由宿主系统执行工具并把结果返回给模型的机制。

## 重点范围

- tool schema 设计
- tool description 如何写清楚调用条件
- tool_choice
- tool result loop
- 多工具调用
- 工具错误处理
- 有副作用工具的权限确认

## 面试表达要点

- 工具不是越多越好，工具面要小、语义清晰、权限可控。
- 对删除、发送、支付、发布、提交代码等有副作用动作，要有人类确认或权限策略。
- 工具错误不应该直接中断，要把错误作为 tool result 返回，让模型尝试恢复或请求澄清。

## 高频问题占位

- [ ] Tool schema 应该怎么设计？
- [ ] 如何处理工具调用失败？
- [ ] 如何避免 Agent 滥用工具？
- [ ] manual loop 和 tool runner 有什么区别？
