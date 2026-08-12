---
type: wiki
status: generated
created: 2026-08-12
updated: 2026-08-12
aliases:
  - Agent System Component Boundaries
  - Agent、RAG、Tool、Skill、MCP
tags:
  - agent-development
  - agent-architecture
  - tool-use
  - rag
  - mcp
confidence: medium
---

# Agent系统组件边界

## 一句话定义

Agent 不是某一个 API 或模型，而是围绕目标建立的决策与执行系统；RAG、Tool、Skill、MCP 和 Runtime 分别解决知识、动作、能力封装、协议互操作和运行控制问题。

## 五个组件的职责

| 组件 | 解决的问题 | 不应该承担的职责 |
|---|---|---|
| Agent | 根据目标和上下文决定下一步 | 直接绕过权限执行副作用动作 |
| RAG | 提供可检索、可更新、可追溯的知识 | 自动保证答案正确 |
| Tool | 让系统访问外部数据或执行动作 | 把所有业务流程塞进一个万能工具 |
| Skill | 将一组方法、提示、工具和约束封装成可复用能力 | 替代 Runtime 的生命周期管理 |
| MCP | 约定模型客户端与工具/资源服务之间的接入方式 | 自动解决授权、数据质量和业务幂等 |

## Agent 与普通 LLM 应用的边界

- 单次分类、摘要、抽取：优先 Single LLM Call。
- 步骤固定、分支可枚举：优先 Workflow。
- 需要模型选择工具、根据结果调整计划：考虑 Tool-using Agent。
- 需要长运行、持久状态、审批和恢复：需要更完整的 Managed Agent Runtime。

判断标准不是“有没有调用模型”，而是系统是否存在可观察的决策循环：

```text
目标
  -> 读取上下文
  -> 决策/规划
  -> 工具或检索
  -> 观察结果
  -> 更新状态
  -> 继续、请求确认或结束
```

## 设计原则

1. 工具小而清晰：一个工具最好对应一个可验证的能力，避免 `do_everything`。
2. 知识与动作分离：RAG 返回证据，Tool 执行动作，Agent 决定何时使用。
3. 协议与实现分离：MCP 负责连接约定，权限、超时、审计和幂等仍由宿主系统负责。
4. Runtime 与 UI 分离：Runtime 输出事件，CLI、Web UI、日志和回放消费同一事件源。
5. 副作用可控：删除、发送、支付、发布、提交代码等操作默认需要显式权限或人工确认。

## 常见误区

- “接了向量库就是 Agent”：这只是 RAG，不代表存在自主决策循环。
- “工具越多越强”：工具面越大，选择错误、权限越界和上下文噪声越严重。
- “MCP 等于安全”：MCP 只解决接入协议，不能替代授权和审计。
- “Skill 就是 prompt”：成熟 Skill 还应包含输入输出契约、工具依赖、限制条件和加载策略。

## 与现有知识的关系

- Runtime：[[30_知识库_Wiki/概念/Agent开发/事件驱动Agent Runtime]]
- Tool：[[30_知识库_Wiki/主题/Agent开发/Tool Use与函数调用]]
- 权限：[[30_知识库_Wiki/概念/Agent开发/Capability-based Tool Policy]]
- RAG 决策：[[30_知识库_Wiki/概念/Agent开发/Agentic RAG检索决策与质量门控]]
- 评估：[[30_知识库_Wiki/概念/Agent开发/Agent可观测性三支柱与轨迹调试]]

