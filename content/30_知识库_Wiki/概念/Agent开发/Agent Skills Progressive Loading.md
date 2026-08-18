---
type: wiki
status: generated
created: 2026-06-26
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

## Skill 的组成

一个可维护的 Skill 通常包含：

- 名称与触发描述。
- 适用和不适用场景。
- 操作步骤与决策规则。
- 所需工具和权限。
- 输入输出约定。
- 示例、模板或辅助脚本。
- 失败处理和验证方法。

Skill 应解决一个相对稳定的工作方法，不要把一次性的任务上下文硬编码进去。

## 三阶段加载

1. **发现**：只加载名称、描述和位置。
2. **选择**：根据当前目标筛选候选 Skill。
3. **展开**：只读取被选中 Skill 的完整指令和资源。

复杂 Skill 还可以继续按章节或资源按需读取，避免一次把所有示例、文档和脚本塞入上下文。

## Skill 与 Tool

- Skill 描述“如何完成一类任务”。
- Tool 提供“可以执行的外部动作”。

Skill 可以指导 Agent 组合多个 Tool，但本身不应绕过 Tool Policy。加载了部署 Skill，不代表自动获得生产发布权限。

## 安全与版本

- Workspace Skill 和第三方 Skill 按不可信内容审查。
- Skill 声明所需 capability，由 Host 决定是否授权。
- 记录本次运行使用的 Skill 版本。
- 更新后通过代表性任务做回归测试。
- Skill 内引用的脚本仍需沙箱和参数校验。

## 常见失败模式

- 描述太泛，任何任务都触发。
- 多个 Skill 高度重叠。
- 加载后永久留在上下文。
- 把凭证和环境细节写进 Skill。
- Skill 指令要求绕过审批或直接执行脚本。

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
- [[30_知识库_Wiki/主题/Agent开发/MCP与工具生态]]
- [[30_知识库_Wiki/概念/Agent开发/Agent Guardrails分层设计]]
