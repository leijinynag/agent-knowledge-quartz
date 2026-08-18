---
type: wiki
status: generated
created: 2026-06-26
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

## 什么时候使用多 Agent

多 Agent 只有在以下情况才值得：

- 子任务需要明显不同的专业上下文或工具。
- 工作可以并行，且聚合成本可控。
- 角色隔离能减少单一上下文污染。
- 独立评审的收益大于额外模型成本。

如果只是让三个角色重复阅读同一批内容，通常单 Agent 加确定性 workflow 更简单。

## 交接契约

```json
{
  "objective": "子任务目标",
  "constraints": ["允许修改的目录"],
  "inputs": ["证据和文件引用"],
  "deliverables": ["patch", "test_report"],
  "acceptance": ["测试通过"],
  "unresolved": []
}
```

交接必须包含事实、证据、约束和未解决问题。不要通过自由聊天传递全部上下文。

## 编排方式

- **顺序交接**：Planner → Implementer → Reviewer。
- **并行 worker**：多个 Agent 分析独立模块，再由 Orchestrator 聚合。
- **Evaluator loop**：生成、评审、有限次数修正。
- **专家路由**：根据任务选择安全、性能或测试 Agent。

Orchestrator 负责预算、取消、重复任务检测和最终验收。

## 冲突处理

- 文件修改按所有权或模块划分。
- 并行结论保留证据，由聚合器决定。
- Reviewer 只提出问题，提交权仍由确定性流程控制。
- 多轮分歧达到上限后转人工或选择保守方案。

## 成本与上下文

每个 Agent 使用独立的最小上下文。共享信息通过 artifact、状态对象或文件引用传递，避免每个角色重复携带完整历史。评估时统计总 token 和 wall-clock 时间，不只看单 Agent 指标。

## 常见失败模式

- 角色名称不同，但 prompt 和工具完全相同。
- Agent 互相转交责任，没有明确交付物。
- Evaluator 不读取真实代码和测试，只评文字说明。
- 并行修改同一文件造成冲突。
- 没有全局预算，子 Agent 递归创建更多 Agent。

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
- [[30_知识库_Wiki/概念/Agent开发/Agent上下文工程]]
- [[30_知识库_Wiki/概念/Agent开发/Workflow与Agent边界]]
