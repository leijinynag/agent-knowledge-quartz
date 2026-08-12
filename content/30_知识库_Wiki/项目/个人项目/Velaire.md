---
type: wiki
status: generated
created: 2026-06-26
updated: 2026-06-26
aliases:
  - Velaire Code
  - Velaire
  - Claude Code Agent 项目
tags:
  - interview/project
  - project/personal
  - agent-development
  - typescript
generated_from:
  - "/Users/bytedance/velaire"
confidence: medium
---

# Velaire

## 一句话介绍

Velaire 是一个基于 **Bun + TypeScript + React Ink + Vite + Commander** 开发的通用 Agent Runtime / Claude Code 类 Agent 项目，支持 CLI/TUI、Web Workbench、工具调用、策略审批、Skills、Provider 适配和多 Agent 协作编码流程。

## 背景与目标

目标不是只做一个“能聊天的代码机器人”，而是实现一个可扩展的 Agent 开发框架：

- 支持终端 TUI 与 Web Workbench 双端使用。
- 支持代码任务分解、代码搜索、Patch 应用、Shell 执行、Todo 规划和用户确认。
- 支持多 Provider 适配，而不是把 Runtime 绑定到单一模型 API。
- 支持策略审批、工具权限、技能加载、运行日志和可观测性。
- 探索 Planner / Generator / Evaluator 多 Agent 协作模式。

## 我的职责

- 设计并实现 Agent Runtime 核心循环。
- 设计工具系统、权限策略和审批机制。
- 设计 Provider Adapter，把不同模型 API 统一成内部事件协议。
- 设计 CLI/TUI 与 Web Workbench 的事件消费方式。
- 设计 Skills / Preset 机制，让不同 Agent 能力以组合方式扩展。
- 设计多 Agent 编码工作流，包括 Planner、Generator、Evaluator 和产物交接。

## 技术架构

### 技术栈

- Runtime / Package Manager：Bun
- Language：TypeScript
- CLI：Commander
- TUI：Ink + React
- Web Workbench：React 19 + Vite
- Schema / Config：Zod、YAML、gray-matter
- Provider SDK：Anthropic SDK、OpenAI-compatible API
- Test：bun test

### 分层结构

- `src/foundation`：消息、模型事件、工具、结果、错误等协议类型。
- `src/runtime`：Agent Runtime、transcript、middleware、timeline、executor。
- `src/policy`：工具权限、风险评估、审批、持久化。
- `src/tools`：文件、Shell、Todo、用户交互等工具。
- `src/providers`：Anthropic、OpenAI-compatible、Mock provider。
- `src/presets`：Coding、Research-lite、多 Agent 编码等预设。
- `src/skills`：Skill 发现、解析、注册和中间件。
- `src/cli`：命令行与 Ink TUI。
- `src/workbench`：Bun Server + React Web Workbench。

## 核心难点

### 1. 通用 Agent Runtime 设计

项目没有把 coding 行为写死在循环里，而是把核心 Runtime 抽象成通用事件循环：接收用户消息、调用 Provider、解析工具调用、执行工具、写入工具结果、继续下一轮，直到结束或达到边界条件。

相关知识页：[[30_知识库_Wiki/概念/Agent开发/事件驱动Agent Runtime]]

### 2. Provider 无关抽象

不同模型 API 在 streaming、tool call、reasoning/thinking、usage metadata 上格式不同。Velaire 通过 Provider Adapter 把外部事件转换成统一的内部 ModelEvent，降低 Runtime 对具体 Provider 的耦合。

相关知识页：[[30_知识库_Wiki/概念/Agent开发/Provider Adapter模型抽象]]

### 3. 工具权限与安全策略

工具调用不是简单执行。工具先声明 capability，策略引擎结合路径、模式、风险和用户配置决定 allow / ask / deny。比如读文件可以默认允许，写文件、Shell、side effect 操作需要确认或被拒绝。

相关知识页：[[30_知识库_Wiki/概念/Agent开发/Capability-based Tool Policy]]

### 4. RuntimeEvent 可观测性

Runtime 通过 `AsyncIterable<RuntimeEvent>` 输出统一事件流，TUI、Workbench、日志和测试都消费同一套事件协议。这让不同 UI 共享同一个 Agent 内核。

相关知识页：[[30_知识库_Wiki/概念/Agent开发/Agent可观测性与RuntimeEvent]]

### 5. 多 Agent 协作编码

多 Agent 不是简单让几个模型互相聊天，而是通过 Planner / Generator / Evaluator 的角色分离、`spec.md` / `task.md` / `evaluation.md` 等显式产物、评审门禁和有限迭代来保证可控性。

相关知识页：[[30_知识库_Wiki/概念/Agent开发/多Agent编码工作流]]

## 关键决策与 trade-off

| 决策 | 收益 | 代价 |
|---|---|---|
| 使用事件驱动 Runtime | UI、日志、测试、回放都能复用同一事件流 | 事件类型设计和状态管理更复杂 |
| Provider Adapter 分层 | 可以支持 Anthropic / OpenAI-compatible / Mock | 需要维护统一抽象和边界转换 |
| 工具 capability + policy | 安全边界系统化，而不是只依赖 prompt | 工具注册和执行链路更长 |
| Skills progressive loading | 减少上下文占用，按需加载能力 | 需要 skill discovery 和触发逻辑 |
| Planner/Generator/Evaluator 分工 | 多 Agent 协作更可控、可评估 | 流程更重，延迟和状态管理成本更高 |

## 结果与量化指标

目前可陈述为：

- 完成 CLI/TUI + Web Workbench 双端 Agent 交互原型。
- 支持 Provider Adapter、工具调用、策略审批、Skills、Preset、多 Agent 编码工作流。
- 建立覆盖 runtime、provider streaming、policy、skills、CLI、TUI、workbench、presets 的测试结构。

待补充：

- [ ] 代码规模、测试数量、覆盖模块数。
- [ ] 一次真实 coding task 的端到端 demo 结果。
- [ ] 与 Claude Code / Codex 类工具相比的差异化点。

## 面试讲述版本

### 1 分钟版

我做了一个叫 Velaire 的个人项目，它是一个 TypeScript/Bun 实现的通用 Agent Runtime，目标是复刻并扩展 Claude Code 类工具。它支持 CLI/TUI 和 Web Workbench 两种交互方式，底层通过统一的 RuntimeEvent 事件流驱动；模型层支持 Anthropic 和 OpenAI-compatible Provider；工具层支持文件读写、Shell、Patch、Todo、用户确认，并通过 capability-based policy 做权限控制。项目里我重点解决了三个问题：通用 Agent 循环怎么设计、工具调用如何安全可控、以及多 Agent 编码如何通过 Planner/Generator/Evaluator 和显式产物进行协作。

### 3 分钟版

Velaire 是我为了深入理解 Agent 开发做的一个个人项目。它不是简单调用 LLM API，而是尝试抽象出一个通用 Agent Runtime。

架构上我把系统分成几层：foundation 定义消息、模型事件、工具结果等协议；runtime 负责 Agent loop 和 transcript；providers 把 Anthropic、OpenAI-compatible 等不同模型 API 转成统一事件；tools 提供文件、Shell、Todo、用户确认等能力；policy 负责工具权限审批；cli 和 workbench 分别提供终端和浏览器 UI。

这个项目比较有价值的点是：第一，Runtime 是事件驱动的，所有过程都通过 RuntimeEvent 输出，所以 TUI、Web Workbench、JSONL 日志和测试可以消费同一套事件协议。第二，我没有把工具直接暴露给模型执行，而是让工具声明 capability，再由 policy engine 判断 allow、ask 还是 deny。第三，我探索了多 Agent 编码模式，把任务拆成 Planner、Generator、Evaluator，通过 spec、task、evaluation 等显式文件进行协作和门禁，而不是让多个 Agent 自由聊天。

如果面试官追问，我可以展开讲 Provider Adapter、Tool Use Loop、策略审批、多 Agent 协作和 Workbench 可观测性设计。

### 深挖版

可围绕以下方向展开：

1. Agent Runtime loop 如何处理 streaming、tool call、tool result、max steps 和 abort。
2. Provider Adapter 如何统一不同模型 API 的事件格式。
3. capability-based policy 如何避免模型直接执行危险工具。
4. RuntimeEvent 如何支持 TUI、Workbench、日志和回放。
5. 多 Agent 编码工作流为什么要引入显式 artifact 和 evaluator gate。

## 高频追问

- 为什么不直接写一个 coding bot，而要抽象 Runtime？
- Provider Adapter 怎么处理不同 API 的 tool call 差异？
- 工具权限怎么设计？哪些操作需要确认？
- Workbench 和 TUI 如何复用同一套 Runtime？
- 多 Agent 协作相比单 Agent 有什么收益和成本？
- 如何避免 Agent 无限循环？
- 如何评估一个 Agent 是否完成了任务？
- 如果用户让 Agent 执行危险 Shell 命令，你怎么拦截？

## 如果重做会怎么改

- 增加更系统的 Eval 数据集，验证 Agent 在真实 coding task 上的成功率。
- 强化运行回放能力，把 RuntimeEvent 与文件 diff、tool result、approval decision 串成可复盘 trace。
- 把多 Agent 协作中的 role、artifact、gate 做成更通用的 workflow DSL。
- 增加更完整的 secret 管理和 MCP 集成能力。

## 相关页面

- [[40_地图与索引_Maps/项目经历 MOC]]
- [[40_地图与索引_Maps/Agent开发面试 MOC]]
- [[30_知识库_Wiki/主题/Agent开发/Agent系统设计]]
- [[30_知识库_Wiki/主题/Agent开发/Tool Use与函数调用]]
- [[30_知识库_Wiki/主题/Agent开发/Agent评估与可观测性]]
- [[30_知识库_Wiki/主题/Agent开发/Agent安全与权限]]
