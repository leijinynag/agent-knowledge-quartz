---
type: wiki
status: generated
created: 2026-06-26
aliases:
  - 工具权限策略
  - Capability-based Policy
tags:
  - agent-development
  - tool-use
  - agent-safety
confidence: medium
---

# Capability-based Tool Policy

## 一句话定义

Capability-based Tool Policy 是让工具先声明自己具备的能力和风险，再由策略引擎根据上下文决定 allow、ask 或 deny 的安全机制。

## 核心要点

- 工具不能只靠名字判断风险，要声明 capability。
- 常见 capability：workspace.read、workspace.write、shell.execute、network、side_effect 等。
- 策略引擎结合路径、模式、用户配置、风险等级和审批历史做决策。
- 对写文件、Shell、外部副作用等操作，默认应该更谨慎。

## 决策输入

```text
主体身份
+ capability
+ 资源作用域
+ 参数摘要
+ 当前任务目标
+ 环境
+ 风险等级
+ 审批状态
→ allow / ask / deny
```

同一个工具在不同参数下风险不同。例如读取工作区内文件可以自动允许，读取用户主目录或密钥文件则应拒绝。

## Capability 设计

能力名称要稳定且可组合：

- `workspace.read`
- `workspace.write`
- `process.execute`
- `network.fetch`
- `message.send`
- `deployment.publish`

资源范围单独表达，例如仓库路径、域名、数据库、频道或环境。不要为每个具体工具名硬编码一套权限规则。

## 审批绑定

审批结果必须绑定：

- 工具和 capability。
- 关键参数哈希。
- 资源范围。
- 有效时间。
- 单次或持续授权。

模型在审批后修改参数时，需要重新决策，不能复用旧批准。

## 默认策略

- 未知工具或未知 capability 默认拒绝。
- 只读不等于无风险，敏感读取仍需限制。
- 生产环境比开发环境更严格。
- 外部副作用默认 ask。
- 不可逆和高价值操作可直接 deny 或要求更强认证。

## 审计

记录策略版本、输入摘要、决策、理由和审批人。这样权限事故发生后才能判断是工具声明错误、策略错误还是审批信息不足。

## 在 Velaire 中的体现

Velaire 中工具执行前会进入 policy 层。读 workspace 可以默认允许，写 workspace、Shell 执行和 side effect 操作通常需要 ask；越界写入或高风险操作可以 deny。

相关项目：[[30_知识库_Wiki/项目/个人项目/Velaire]]

## 面试表达

> 我不把工具安全完全交给 prompt，而是在系统层设计 capability-based policy。模型只能提出工具调用，真正是否执行由策略引擎决定，这样可以把权限边界、审批和审计做成工程机制。

## 高频追问

- 哪些工具需要用户确认？
- 如何处理用户永久允许某类操作？
- 如何避免 prompt injection 诱导危险工具调用？
- policy 和 tool schema 的边界是什么？

## 相关页面

- [[30_知识库_Wiki/主题/Agent开发/Agent安全与权限]]
- [[30_知识库_Wiki/主题/Agent开发/Tool Use与函数调用]]
- [[30_知识库_Wiki/概念/Agent开发/Agent风险分级与人工审批]]
- [[30_知识库_Wiki/概念/Agent开发/Tool Schema设计]]
