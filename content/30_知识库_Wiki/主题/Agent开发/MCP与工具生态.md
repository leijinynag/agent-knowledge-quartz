---
type: wiki
status: stable
created: 2026-06-26
aliases:
  - MCP
  - Model Context Protocol
  - 工具生态
tags:
  - interview/agent
  - mcp
confidence: high
---

# MCP与工具生态

## 一句话定义

MCP 是连接 AI 应用与外部能力的标准协议，使 Host 能通过 Client 发现和调用 Server 暴露的工具、资源和提示模板，而不需要为每个组合编写专用集成。

## 基本角色

```text
Host（桌面应用、IDE、Agent 服务）
  ├─ MCP Client A ↔ MCP Server A
  ├─ MCP Client B ↔ MCP Server B
  └─ MCP Client C ↔ MCP Server C
```

- **Host**：负责用户体验、模型、权限和上下文。
- **Client**：维护与一个 Server 的协议连接。
- **Server**：暴露一组受控能力。

MCP 不替代 Agent Runtime，也不负责模型推理。它解决的是能力如何被发现、描述和调用。

## 三类常见能力

### Tools

可执行动作，例如查询 issue、运行数据库查询或创建文档。通常由模型选择是否调用，但 Host 必须执行权限检查。

### Resources

可读取的上下文资源，例如文件、数据库 schema、文档或日志。它更像标准化的数据访问入口。

### Prompts

可复用的提示模板或工作流入口，通常由用户或 Host 显式选择。

三者不要混用。读取文档不一定要包装成有副作用的工具，固定模板也不应伪装成模型自主工具。

## 生命周期

一个 MCP 连接通常经历：

1. 建立传输连接。
2. 协商协议版本与能力。
3. 初始化 Server 信息。
4. 列出工具、资源或 prompts。
5. 按需调用。
6. 处理通知、取消和错误。
7. 关闭连接。

Host 需要处理 Server 不可用、能力变化、超时和版本不兼容。

详见：[[30_知识库_Wiki/概念/Agent开发/MCP架构与生命周期]]

## MCP 与 Function Calling

| 维度 | Function Calling | MCP |
|---|---|---|
| 关注点 | 模型如何表达工具调用 | 应用如何连接外部能力 |
| 工具定义 | 通常写在应用代码中 | Server 可被多个 Host 复用 |
| 发现能力 | 应用预先注入 | 协议化列举 |
| 权限责任 | 应用负责 | Host 与 Server 共同负责 |
| 传输 | 由应用自定 | 使用协议定义的连接方式 |

实际链路通常是：Host 从 MCP Server 获得工具描述，将当前允许的工具交给模型，模型输出 function call，再由 Host 通过 MCP Client 执行。

## Server 设计原则

- 以业务动作命名，不暴露底层通用执行器。
- 参数 schema 尽量封闭，限制路径、域名和枚举。
- 返回结构包含状态、数据、错误、分页和截断信息。
- 只返回任务所需的数据，不倾倒整个数据库对象。
- 明确只读与写入工具。
- 支持取消、超时和幂等。
- 日志可关联 Host 的 trace 和调用身份。

## 工具发现与裁剪

连接多个 Server 后，工具数量可能迅速膨胀。Host 可以：

- 按任务、权限和工作区过滤。
- 先根据名称和描述做能力检索。
- 只把候选工具的完整 schema 放进当前上下文。
- 对相似工具做命名治理和选择评估。
- 缓存稳定能力列表，但处理 Server 变更通知。

## 安全边界

MCP Server 是外部执行边界，不应默认可信：

- Host 保留最终授权权。
- Server 凭证不进入模型上下文。
- 远程 Server 使用明确身份和最小 scope。
- 返回内容按不可信数据处理。
- 对本地 Server 限制文件、进程和网络范围。
- 用户应能看到连接了哪些 Server 及其能力。

## 什么时候不需要 MCP

- 应用只有一两个稳定的内部工具。
- 工具不会被多个 Host 复用。
- 引入协议层的部署和权限成本高于收益。
- 业务需要强事务，直接领域 API 更清楚。

MCP 是集成标准，不是所有工具调用的强制前提。

## 常见失败模式

- 把 MCP 当成 Agent 框架。
- 一个 Server 暴露万能 shell 或万能 HTTP 请求。
- Host 把所有 Server 工具一次性给模型。
- Server 返回大量无关数据，挤占上下文。
- 只做连接，不做身份、权限和审计。
- 工具升级后没有版本兼容和弃用策略。

## 面试表达

MCP 解决的是 Host 和外部能力之间的标准化集成，function calling 解决模型如何表达一次工具调用。我的 Host 会从 Server 发现能力，但只把当前任务有权限且相关的工具交给模型；真正执行前仍经过本地策略、审批和审计。

## 相关页面

- [[30_知识库_Wiki/主题/Agent开发/Tool Use与函数调用]]
- [[30_知识库_Wiki/概念/Agent开发/Tool Schema设计]]
- [[30_知识库_Wiki/概念/Agent开发/Agent Skills Progressive Loading]]
- [[30_知识库_Wiki/主题/Agent开发/Agent安全与权限]]
