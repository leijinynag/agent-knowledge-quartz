---
type: wiki
status: generated
created: 2026-08-12
updated: 2026-08-12
aliases:
  - Agent Loop
  - Agent循环
  - Agent终止条件
tags:
  - interview/agent
  - agent-runtime
  - agent-loop
confidence: high
---

# Agent Loop与终止条件

## 一句话定义

Agent Loop 是模型在“观察状态、选择行动、执行工具、吸收结果、判断是否继续”之间反复迭代的控制循环；终止条件负责让循环在任务完成、无法继续或风险超界时可靠结束。

## 核心要点

- Agent 的自主性来自“下一步行动由模型根据当前状态动态决定”，而不是单纯调用过 LLM。
- Runtime 应控制循环边界，模型只负责提出动作，不能拥有无限执行权。
- 成功结束、受控中止和等待用户输入都是合法终态。
- 每一轮都应产生可记录、可回放的状态变化。
- 终止判断必须结合模型声明与程序约束，不能只相信模型说“完成了”。

## 基本状态机

```text
RECEIVE_GOAL
    ↓
BUILD_CONTEXT
    ↓
MODEL_DECISION
    ├─ tool_call → VALIDATE → EXECUTE → OBSERVE ─┐
    ├─ ask_user → WAITING_USER                   │
    ├─ final     → VERIFY → COMPLETED            │
    └─ invalid   → RECOVER / FAILED              │
                                                  │
                    └─────────────────────────────┘
```

Runtime 至少需要保存：

- `goal`：用户目标及验收条件。
- `messages`：当前模型可见的上下文。
- `working_state`：计划、已完成步骤、关键中间结果。
- `tool_calls`：工具名、参数、结果、耗时和错误。
- `budget`：轮数、token、时间和费用预算。
- `status`：运行中、等待确认、完成、失败或取消。

## 终止条件设计

### 成功终止

- 产物通过确定性校验，例如测试通过、文件生成、数据满足 schema。
- Runtime 根据验收规则确认目标已完成。
- 模型提交 final，同时不存在未完成的必要步骤。

### 受控中止

- 达到最大轮数、最大工具调用数、token 或时间预算。
- 连续多轮没有有效状态变化。
- 同类工具错误重复发生，且恢复策略已经耗尽。
- 权限策略拒绝执行高风险动作。
- 用户主动取消任务。

### 暂停并等待

- 缺少无法合理推断的关键输入。
- 工具需要用户授权或高风险操作确认。
- 出现多个业务含义不同且代价较高的选择。

## 防止无限循环

不要只设置 `max_iterations`，还应检测进展：

```text
progress = 新增事实 + 完成子目标 + 状态变化 + 可验证产物
```

可组合的保护机制：

- 对最近若干轮的工具名、参数和结果做去重检测。
- 连续失败时执行指数退避或切换恢复策略。
- 记录未完成子目标，禁止模型反复“重新规划”却不执行。
- 给每个工具设置调用预算和幂等键。
- 在关键阶段调用 verifier，而不是让执行模型自我确认。

## Workflow 与 Agent Loop

| 维度 | Workflow | Agent Loop |
|---|---|---|
| 下一步 | 代码预先定义 | 模型动态选择 |
| 可预测性 | 高 | 较低 |
| 灵活性 | 较低 | 高 |
| 测试重点 | 节点输入输出 | 轨迹、工具和终止 |
| 适用任务 | 稳定固定流程 | 路径难以预先枚举 |

工程上可把两者组合：外层使用确定性 workflow 管理阶段，阶段内部使用受限 Agent Loop 处理开放问题。

## 面试表达

设计 Agent Runtime 时，我会把模型视为策略生成器，把 Runtime 视为控制平面。Runtime 负责校验工具调用、维护状态、执行预算、记录事件并决定是否允许继续。终止条件由完成校验、预算限制、无进展检测、权限边界和人工确认共同构成。

## 与其他概念的关系

- 上位概念：[[30_知识库_Wiki/主题/Agent开发/Agent系统设计]]
- Runtime 实现：[[30_知识库_Wiki/概念/Agent开发/事件驱动Agent Runtime]]
- 轨迹记录：[[30_知识库_Wiki/概念/Agent开发/Agent可观测性与RuntimeEvent]]
- 工具执行：[[30_知识库_Wiki/主题/Agent开发/Tool Use与函数调用]]
- 安全边界：[[30_知识库_Wiki/主题/Agent开发/Agent安全与权限]]
- 评估方法：[[30_知识库_Wiki/概念/Agent开发/Agent轨迹评估]]

## 待澄清

- 针对不同任务类型，无进展检测阈值应如何自动调整。
