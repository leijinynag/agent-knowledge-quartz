---
type: wiki
status: generated
created: 2026-08-12
updated: 2026-08-12
aliases:
  - Agent Prompt Composer
  - Agent输入框
  - Agent快捷指令
tags:
  - interview/agent
  - frontend
  - agent-ui
  - interaction
confidence: medium
---

# Agent前端输入框与快捷指令设计

## 我的理解

Agent 的输入框不只是一个 textarea。普通聊天输入框只负责把用户文本发出去，Agent 输入框还要承载目标描述、上下文选择、文件引用、工具模式、快捷动作和风险提示。

我会把它当成“任务提交器”，不是单纯的“消息发送器”。

## 输入框应该支持什么

我会优先支持这些能力：

| 能力 | 作用 |
|---|---|
| 多行输入 | 用户可以描述复杂任务 |
| 文件/链接引用 | 明确告诉 Agent 哪些内容可以看 |
| 快捷指令 | 快速套用常见任务模板 |
| 模式切换 | 比如问答、改写、执行、研究 |
| 草稿保存 | 刷新页面不丢输入 |
| 发送前检查 | 空输入、超长输入、缺少上下文时提醒 |

核心不是把输入框做复杂，而是让用户更容易表达“我要你做什么、基于什么做、做到什么程度”。

## 快捷指令怎么设计

快捷指令不要只是把一段固定 prompt 塞进去。我更倾向于把它设计成结构化模板：

```ts
type ShortcutCommand = {
  id: string
  title: string
  intent: "summarize" | "debug" | "rewrite" | "plan" | "review"
  requiredContext?: "file" | "selection" | "url"
  defaultPrompt: string
  riskLevel: "low" | "medium" | "high"
}
```

例如“审查这段代码”这个指令，应该检查用户有没有选中文件或粘贴代码；“帮我执行修改”则要提示可能触发工具调用。

## 发送前我会做的校验

- 当前是否已有 run 在执行。
- 输入是否为空或只有空白字符。
- 是否引用了不存在或无权限访问的文件。
- 是否超出上下文预算。
- 当前模式是否允许调用工具。
- 是否需要先让用户确认执行范围。

这里的校验不是为了阻止用户，而是避免提交一个 Agent 很难完成的模糊任务。

## 输入态和执行态分开

我会把输入框状态和 run 状态分开：

```ts
type ComposerState = {
  text: string
  attachments: AttachmentRef[]
  selectedCommand?: string
  mode: "chat" | "agent" | "review" | "edit"
  draftSavedAt?: string
}
```

输入框不应该直接被 `isLoading` 粗暴禁用。比如 Agent 正在执行时，用户可能还想继续写下一条草稿，或者补充信息等待 Agent 请求。

## 常见失败方式

- 只做一个 textarea，所有任务都靠用户自己描述清楚。
- 快捷指令变成 prompt 菜单，没有上下文校验。
- 正在运行时直接禁用输入框，用户无法准备下一步。
- 文件引用只显示文件名，不显示权限和是否已加载。
- 输入过长后直接失败，没有提前提示压缩或选择重点。

## 面试表达

我会把 Agent 输入框设计成任务提交器。它除了文本，还要管理上下文引用、快捷指令、模式、草稿和发送前校验。快捷指令最好是结构化模板，而不是简单 prompt 文案；输入态和执行态也要分开，这样运行中还能保存草稿、补充上下文或准备下一步。

## 相关笔记

- [[30_知识库_Wiki/概念/Agent开发/Agent对话UI状态机]]
- [[30_知识库_Wiki/概念/Agent开发/Agent会话历史与前端本地缓存]]
- [[30_知识库_Wiki/概念/Agent开发/Agent上下文工程]]
- [[30_知识库_Wiki/概念/Agent开发/Agent风险分级与人工审批]]
