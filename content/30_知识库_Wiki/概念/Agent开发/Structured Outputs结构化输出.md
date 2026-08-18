---
type: wiki
status: stable
created: 2026-08-18
aliases:
  - Structured Outputs
  - 结构化输出
tags:
  - interview/agent
  - llm-app
confidence: high
---

# Structured Outputs结构化输出

## 一句话定义

Structured Outputs 是让模型按照明确 schema 返回可被程序消费的数据，用来减少格式漂移，但它只能保证结构约束，不能自动保证事实和业务语义正确。

## 适用场景

- 分类与路由。
- 实体和字段抽取。
- 任务计划与检查清单。
- 最终 API 响应。
- 评估分数与理由。
- 工具参数生成。

## Schema 设计原则

- 字段少而明确，不让一个字段承担多种含义。
- 能用枚举就不用自由文本。
- 区分必填、可选和 `null`。
- 对数组设置合理的数量边界。
- 描述业务语义，不只描述数据类型。
- 使用版本字段管理长期演进。

```json
{
  "status": "complete | needs_input | failed",
  "summary": "string",
  "evidence_ids": ["string"],
  "next_action": "string | null"
}
```

## 两层校验

### 语法校验

检查类型、必填字段、枚举和嵌套结构。

### 业务校验

检查实体是否存在、数值是否在权限范围内、引用是否有效、状态迁移是否合法。

模型可能输出结构完全正确但业务完全错误的数据，因此两层都需要。

## 修复策略

1. 首次输出不符合 schema 时，返回具体校验错误。
2. 只允许有限次数的格式修复。
3. 修复仍失败时，降级到人工或确定性解析。
4. 记录原始输出和修复轨迹用于评估。

## 与 Tool Calling 的区别

- Tool Calling 表达“下一步要执行哪个动作”。
- Structured Output 表达“这次调用最终要返回什么结构”。

Agent 可以在中间多轮调用工具，最后再生成结构化结果。不要把最终输出 schema 伪装成真实业务工具，否则轨迹和权限语义会变混乱。

## 常见失败模式

- schema 过深，模型频繁遗漏字段。
- 字段描述含糊，格式正确但语义错。
- 把大量自然语言塞进单个字段。
- schema 变更后消费方没有版本兼容。
- 解析成功就直接写数据库，没有业务校验。

## 面试表达

我会对进入程序流程的模型输出使用 schema，并在 schema 校验后继续做业务校验。格式失败可以有限修复，但不会无限重试；工具调用和最终结构化响应也会分开建模，避免把执行动作与结果合同混为一谈。

## 相关页面

- [[30_知识库_Wiki/主题/Agent开发/LLM应用开发]]
- [[30_知识库_Wiki/概念/Agent开发/Tool Schema设计]]
- [[30_知识库_Wiki/概念/Agent开发/Provider Adapter模型抽象]]
