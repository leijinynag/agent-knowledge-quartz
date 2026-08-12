---
type: wiki
status: draft
created: 2026-06-26
updated: 2026-06-26
aliases:
  - Agent Safety
  - Agent Permissions
  - Agent安全
tags:
  - interview/agent
  - agent-safety
confidence: medium
---

# Agent安全与权限

## 重点范围

- Prompt injection
- Tool permission
- Human-in-the-loop
- Secret 管理
- 最小权限原则
- 有副作用工具确认
- 沙箱环境
- 审计日志

## 面试表达要点

Agent 安全的核心不是“让模型保证不犯错”，而是系统层面限制可执行动作：工具权限、沙箱、确认机制、凭证隔离、审计和回滚。

## 高频问题占位

- [ ] 如何防 Prompt Injection？
- [ ] 哪些工具需要用户确认？
- [ ] Secret 为什么不能放进 prompt？
- [ ] 如何设计 Agent 的权限边界？
