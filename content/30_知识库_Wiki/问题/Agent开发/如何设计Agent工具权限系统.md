---
type: wiki
status: generated
created: 2026-06-26
aliases:
  - Agent工具权限设计题
tags:
  - interview/question
  - agent-safety
  - tool-use
confidence: medium
---

# 问题：如何设计Agent工具权限系统？

## 30秒回答

我不会只依赖 prompt 约束模型，而会在系统层做工具权限控制。每个工具声明 capability 和风险，策略引擎根据工具类型、路径、用户配置、运行模式和审批历史决定 allow、ask 或 deny。模型只能提出工具调用，是否执行由系统决定。

## 系统化回答

工具权限系统可以分四层：

1. Tool capability：工具声明自己会读、写、执行 shell、访问网络还是产生外部副作用。
2. Policy engine：根据 capability、路径、风险、模式做决策。
3. Approval manager：对需要确认的工具调用发起用户审批。
4. Audit log：记录工具调用、审批结果和执行结果。

## 关键 trade-off

- 默认允许读操作，提高效率。
- 写文件、Shell、外部副作用默认 ask。
- 越界路径、危险命令、secret 访问默认 deny。
- 允许用户配置项目级 allowlist，但要可撤销和可审计。

## 可靠性与安全

- Secret 不进入 prompt。
- 高风险工具必须 human-in-the-loop。
- 工具结果需要 redaction。
- Prompt injection 不能绕过系统层 policy。

## 项目例子

- [[30_知识库_Wiki/项目/个人项目/Velaire]]
- [[30_知识库_Wiki/概念/Agent开发/Capability-based Tool Policy]]

## 高频追问

- 如何判断 shell 命令是否危险？
- 用户点了永久允许怎么办？
- 如何记录审计日志？
- 如果模型伪造用户确认怎么办？
