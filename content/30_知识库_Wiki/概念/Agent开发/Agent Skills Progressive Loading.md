---
type: wiki
status: generated
created: 2026-06-26
updated: 2026-07-15
aliases:
  - Skills Progressive Loading
  - Agent Skills
tags:
  - agent-development
  - skills
  - context-management
confidence: medium
---

# Agent Skills Progressive Loading

## 一句话定义

Skills Progressive Loading 是一种按需加载 Agent 专业能力的上下文管理模式：先只暴露技能索引，等任务相关时再读取完整技能文件。

## 核心要点

- Skill 通常是带 frontmatter 的 Markdown 指令包。
- 系统 prompt 先提供技能名称、描述和触发条件。
- 当任务命中某个 skill 时，再读取完整 `SKILL.md`。
- 好处是减少固定上下文占用，同时保留专业能力可发现性。

## 在 Velaire 中的体现

Velaire 支持从 workspace、用户目录和 repo 内置 skills 中发现技能，并通过 middleware 注入可用 skill 索引。Coding plan、deep research plan 等能力可以作为 skill 存在。

相关项目：[[30_知识库_Wiki/项目/个人项目/Velaire]]

## 面试表达

> 我把复杂任务指令从主系统 prompt 中拆出来做成 skill。主上下文只放 skill 索引，真正需要时再读取完整技能，这样可以降低上下文成本，也让能力扩展更模块化。

## 高频追问

- Skill 和普通 prompt 有什么区别？
- 如何判断什么时候加载 skill？
- Skill 内容太多怎么办？
- Skill 是否会引入 prompt injection 风险？

## 相关页面

- [[30_知识库_Wiki/主题/Agent开发/Agent记忆与上下文管理]]
- [[30_知识库_Wiki/主题/Agent开发/Agent系统设计]]
