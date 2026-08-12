---
type: wiki
status: generated
created: 2026-06-26
updated: 2026-06-26
aliases:
  - Multi-agent Coding Workflow
  - Planner Generator Evaluator
tags:
  - agent-development
  - multi-agent
  - coding-agent
confidence: medium
---

# 多Agent编码工作流

## 一句话定义

多 Agent 编码工作流是把需求澄清、方案规划、代码生成和结果评估拆给不同角色，通过显式产物和评审门禁协作完成代码任务。

## 核心角色

- Planner：澄清需求，生成 spec 和 task plan。
- Generator：根据任务计划实现代码。
- Evaluator：检查方案和实现是否满足要求。

## 核心要点

- 不只是“多个模型互相聊天”，而是有角色边界和产物交接。
- 显式产物可以是 `spec.md`、`task.md`、`generator-notes.md`、`evaluation.md`、`state.json`。
- Evaluator gate 可以防止 Generator 直接把不完整实现交付。
- 需要 max iterations 避免无限循环。

## 在 Velaire 中的体现

Velaire 的多 Agent 编码 harness 设计了 Planner / Generator / Evaluator 角色，并通过文件产物和评审状态驱动迭代。

相关项目：[[30_知识库_Wiki/项目/个人项目/Velaire]]

## 面试表达

> 我认为多 Agent 的关键不是数量，而是协作协议。Velaire 中我用 Planner、Generator、Evaluator 做角色分离，并用显式 artifact 传递上下文，这样比自由聊天更容易评估和调试。

## 高频追问

- 多 Agent 相比单 Agent 的收益是什么？
- 如何避免多 Agent 互相甩锅或循环？
- Evaluator 的标准如何设计？
- 多 Agent 的上下文如何共享？

## 相关页面

- [[30_知识库_Wiki/主题/Agent开发/Agent系统设计]]
- [[30_知识库_Wiki/主题/Agent开发/Agent评估与可观测性]]
