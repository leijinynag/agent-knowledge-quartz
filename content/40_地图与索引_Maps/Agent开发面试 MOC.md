---
type: moc
status: active
created: 2026-06-26
updated: 2026-08-12
aliases:
  - Agent Development Interview MOC
  - Agent开发面试
tags:
  - interview/agent
  - agent-development/moc
---

# Agent开发面试 MOC

## 主题入口

- [[30_知识库_Wiki/主题/Agent开发/Agent开发路线]]
- [[30_知识库_Wiki/主题/Agent开发/LLM应用开发]]
- [[30_知识库_Wiki/主题/Agent开发/Agent系统设计]]
- [[30_知识库_Wiki/主题/Agent开发/Tool Use与函数调用]]
- [[30_知识库_Wiki/主题/Agent开发/MCP与工具生态]]
- [[30_知识库_Wiki/主题/Agent开发/Agent记忆与上下文管理]]
- [[30_知识库_Wiki/主题/Agent开发/Agent评估与可观测性]]
- [[30_知识库_Wiki/主题/Agent开发/Agent安全与权限]]

## 项目锚点

- [[30_知识库_Wiki/项目/个人项目/Velaire]]
- [[30_知识库_Wiki/项目/个人项目/简历项目雷达]]

## Velaire 相关概念页

- [[30_知识库_Wiki/概念/Agent开发/事件驱动Agent Runtime]]
- [[30_知识库_Wiki/概念/Agent开发/Provider Adapter模型抽象]]
- [[30_知识库_Wiki/概念/Agent开发/Capability-based Tool Policy]]
- [[30_知识库_Wiki/概念/Agent开发/Agent Skills Progressive Loading]]
- [[30_知识库_Wiki/概念/Agent开发/多Agent编码工作流]]
- [[30_知识库_Wiki/概念/Agent开发/Agent可观测性与RuntimeEvent]]

## Agent 工程基础

- [[30_知识库_Wiki/概念/Agent开发/Agent Loop与终止条件]]
- [[30_知识库_Wiki/概念/Agent开发/Agent规划范式与动态重规划]]
- [[30_知识库_Wiki/概念/Agent开发/Agent工具执行的幂等重试与超时]]
- [[30_知识库_Wiki/概念/Agent开发/Agent上下文工程]]
- [[30_知识库_Wiki/概念/Agent开发/Agent长期记忆写入与遗忘策略]]
- [[30_知识库_Wiki/概念/Agent开发/Agent风险分级与人工审批]]
- [[30_知识库_Wiki/概念/Agent开发/Agent提示注入与不可信内容隔离]]
- [[30_知识库_Wiki/概念/Agent开发/Agent持久化执行与断点恢复]]
- [[30_知识库_Wiki/概念/Agent开发/Agent轨迹评估]]
- [[30_知识库_Wiki/概念/Agent开发/Agent系统组件边界]]
- [[30_知识库_Wiki/概念/Agent开发/Agentic RAG检索决策与质量门控]]
- [[30_知识库_Wiki/概念/Agent开发/Agent可观测性三支柱与轨迹调试]]
- [[30_知识库_Wiki/概念/Agent开发/Agent测试用例生成与回归集沉淀]]
- [[30_知识库_Wiki/概念/Agent开发/Agent成本预算与Token治理]]
- [[30_知识库_Wiki/概念/Agent开发/前端流式Agent交互]]

## 前端 Agent 交互

- [[30_知识库_Wiki/概念/Agent开发/Agent对话UI状态机]]
- [[30_知识库_Wiki/概念/Agent开发/Agent前端输入框与快捷指令设计]]
- [[30_知识库_Wiki/概念/Agent开发/Agent工具调用确认面板]]
- [[30_知识库_Wiki/概念/Agent开发/Agent工具结果展示与折叠策略]]
- [[30_知识库_Wiki/概念/Agent开发/Agent多轮任务的进度表达]]
- [[30_知识库_Wiki/概念/Agent开发/流式Markdown渲染与代码块中间态]]
- [[30_知识库_Wiki/概念/Agent开发/Agent错误恢复与用户可理解反馈]]
- [[30_知识库_Wiki/概念/Agent开发/Agent会话历史与前端本地缓存]]

## 面试题库

- [[30_知识库_Wiki/问题/Agent开发/如何设计一个可扩展Agent Runtime]]
- [[30_知识库_Wiki/问题/Agent开发/如何设计Agent工具权限系统]]
- [[30_知识库_Wiki/问题/Agent开发/如何做Provider无关的模型适配层]]
- [[30_知识库_Wiki/问题/Agent开发/如何设计多Agent协作编码系统]]
- [[30_知识库_Wiki/问题/Agent开发/如何设计Agent的事件日志与回放]]

## 核心判断框架

不是所有 LLM 应用都应该做成 Agent：

- 简单分类、摘要、抽取、问答：优先 Single LLM Call。
- 固定多步骤流程：优先 Workflow。
- 需要模型自主选择工具、探索、恢复错误：考虑 Tool-using Agent。
- 需要托管执行环境、长运行会话、文件/仓库工作区：考虑 Managed Agent。

## 需要重点准备的能力

- 能设计 tool schema，并解释 tool result loop。
- 能说明 streaming、structured output、prompt caching、token counting 的用途。
- 能讲清楚 MCP、memory、权限确认、人类在回路中的必要性。
- 能设计一个可观测、可恢复、可评估的 Agent 系统。
