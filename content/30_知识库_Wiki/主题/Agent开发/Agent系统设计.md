---
type: wiki
status: draft
created: 2026-06-26
updated: 2026-06-26
aliases:
  - Agent Architecture
  - Agent系统设计
tags:
  - interview/agent
  - agent-architecture
confidence: medium
---

# Agent系统设计

## Agent 基本循环

1. 接收目标
2. 理解上下文
3. 规划下一步
4. 选择工具
5. 执行工具
6. 观察结果
7. 更新状态
8. 判断继续或结束

## 设计问题清单

- Agent 有哪些工具？
- 每个工具的权限边界是什么？
- 什么时候需要用户确认？
- 上下文太长怎么办？
- 工具失败如何恢复？
- 如何避免无限循环？
- 如何判断任务完成？
- 如何记录、回放和评估？

## 常见系统设计题

- [ ] 设计一个代码审查 Agent。
- [ ] 设计一个知识库问答 Agent。
- [ ] 设计一个网页自动化 Agent。
- [ ] 设计一个多 Agent 研究系统。
- [ ] 设计一个可观测的 Agent 平台。
