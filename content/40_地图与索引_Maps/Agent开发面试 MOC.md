---
type: moc
status: active
created: 2026-06-26
aliases:
  - Agent Development Interview MOC
  - Agent开发面试
tags:
  - interview/agent
  - agent-development/moc
---

# Agent开发面试 MOC

## 从这里开始

- [[30_知识库_Wiki/主题/Agent开发/Agent开发路线]]
- [[30_知识库_Wiki/概念/Agent开发/Workflow与Agent边界]]
- [[30_知识库_Wiki/主题/Agent开发/LLM应用开发]]
- [[30_知识库_Wiki/主题/Agent开发/Agent系统设计]]

## LLM 应用基础

- [[30_知识库_Wiki/概念/Agent开发/Structured Outputs结构化输出]]
- [[30_知识库_Wiki/概念/Agent开发/Prompt Caching与上下文缓存]]
- [[30_知识库_Wiki/概念/Agent开发/Agent模型路由与降级]]
- [[30_知识库_Wiki/概念/Agent开发/Provider Adapter模型抽象]]
- [[30_知识库_Wiki/概念/Agent开发/SSE流式Agent状态同步]]

## Tool Use 与能力接入

- [[30_知识库_Wiki/主题/Agent开发/Tool Use与函数调用]]
- [[30_知识库_Wiki/概念/Agent开发/Tool Schema设计]]
- [[30_知识库_Wiki/主题/Agent开发/MCP与工具生态]]
- [[30_知识库_Wiki/概念/Agent开发/MCP架构与生命周期]]
- [[30_知识库_Wiki/概念/Agent开发/Agent Skills Progressive Loading]]
- [[30_知识库_Wiki/概念/Agent开发/Capability-based Tool Policy]]
- [[30_知识库_Wiki/概念/Agent开发/Agent工具执行的幂等重试与超时]]

## Runtime 与执行控制

- [[30_知识库_Wiki/概念/Agent开发/Agent系统组件边界]]
- [[30_知识库_Wiki/概念/Agent开发/事件驱动Agent Runtime]]
- [[30_知识库_Wiki/概念/Agent开发/Agent Loop与终止条件]]
- [[30_知识库_Wiki/概念/Agent开发/Agent规划范式与动态重规划]]
- [[30_知识库_Wiki/概念/Agent开发/Agent状态管理与Checkpoint]]
- [[30_知识库_Wiki/概念/Agent开发/Agent持久化执行与断点恢复]]
- [[30_知识库_Wiki/概念/Agent开发/多Agent编码工作流]]

## Context、Memory 与 RAG

- [[30_知识库_Wiki/主题/Agent开发/Agent记忆与上下文管理]]
- [[30_知识库_Wiki/概念/Agent开发/Agent上下文工程]]
- [[30_知识库_Wiki/概念/Agent开发/Agent长期记忆写入与遗忘策略]]
- [[30_知识库_Wiki/概念/Agent开发/Agentic RAG检索决策与质量门控]]

## 评估、观测与运维

- [[30_知识库_Wiki/主题/Agent开发/Agent评估与可观测性]]
- [[30_知识库_Wiki/概念/Agent开发/Agent轨迹评估]]
- [[30_知识库_Wiki/概念/Agent开发/Agent可观测性与RuntimeEvent]]
- [[30_知识库_Wiki/概念/Agent开发/Agent可观测性三支柱与轨迹调试]]
- [[30_知识库_Wiki/概念/Agent开发/Agent基准测试与离线评估]]
- [[30_知识库_Wiki/概念/Agent开发/Agent测试用例生成与回归集沉淀]]
- [[30_知识库_Wiki/概念/Agent开发/Agent生产故障分类与运行手册]]
- [[30_知识库_Wiki/概念/Agent开发/Agent成本预算与Token治理]]

## 安全与权限

- [[30_知识库_Wiki/主题/Agent开发/Agent安全与权限]]
- [[30_知识库_Wiki/概念/Agent开发/Agent Guardrails分层设计]]
- [[30_知识库_Wiki/概念/Agent开发/Agent提示注入与不可信内容隔离]]
- [[30_知识库_Wiki/概念/Agent开发/Agent风险分级与人工审批]]
- [[30_知识库_Wiki/概念/Agent开发/Agent工具调用确认面板]]

## 项目锚点

- [[30_知识库_Wiki/项目/个人项目/Velaire]]
- [[30_知识库_Wiki/项目/个人项目/简历项目雷达]]

## 前端 Agent 交互

- [[30_知识库_Wiki/概念/Agent开发/前端流式Agent交互]]
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
- 需要多个专业角色时，先证明单 Agent 加工具无法满足，再引入多 Agent。

## 需要重点准备的能力

- 能划清 Workflow 与 Agent 的边界。
- 能设计 tool schema，并解释完整 tool result loop。
- 能说明 streaming、structured output、prompt caching 和模型路由。
- 能讲清楚 MCP、memory、RAG、权限与人工审批的边界。
- 能设计带 checkpoint、trace、评估和降级的 Agent Runtime。
- 能从一次线上失败反推监控、运行手册和回归用例。
