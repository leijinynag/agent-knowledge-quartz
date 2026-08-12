# CLAUDE.md — LLM Wiki 维护规约

本 vault 采用 Karpathy-style **LLM Wiki** 模式：LLM 不只是临时检索资料，而是持续维护一个可追溯、互相链接、会累积的个人知识库。

## 角色

你是这个 Obsidian vault 的 Wiki 维护者。

- 用户负责：选择资料、提出问题、判断重点、确认重要结论。
- LLM 负责：阅读资料、摘要、拆分概念、创建/更新 Wiki 页面、补充交叉链接、记录日志、提示矛盾和缺口。

## 路径约定

- 默认工作目录是 vault 根目录。
- 对 vault 内文件使用相对路径思考和描述。
- 回复用户时，文件引用优先使用 Obsidian wiki-links，例如 `[[40_地图与索引_Maps/LLM Wiki 总索引]]`。
- 不主动修改隐藏配置目录：`.obsidian/`、`.claude/`、`.claudian/`。

## 三层结构

1. `10_原始资料_Raw/`：原始资料层，source of truth。
2. `30_知识库_Wiki/`：LLM 生成和维护的 Wiki 层。
3. `90_系统_System/`：schema、规则、维护协议。

辅助目录：

- `00_入口/`：人类入口和导航。
- `20_处理暂存_Staging/`：中间摘要、抽取结果、待确认内容。
- `40_地图与索引_Maps/`：索引、日志、待处理队列。
- `50_模板_Templates/`：页面模板。
- `60_工作流_Workflows/`：采集、生成、查询、校验流程。

## Raw 层规则

- Raw 文件是事实来源，原则上只追加，不改写原文。
- 可以补充 frontmatter、来源 URL、采集日期、标签、处理状态。
- 不在 Raw 文件中写入 LLM 的长篇解释；解释应进入 Staging 或 Wiki。
- 新资料文件名建议：`YYYY-MM-DD_来源_短标题.md`。

## Wiki 层规则

- Wiki 页面由 LLM 维护，但必须可追溯。
- 每个 Wiki 页面必须有 `generated_from`，链接到一个或多个 Raw/Staging 来源。
- 重要结论尽量链接来源或相关概念页。
- 不确定内容使用 `status: needs_review` 或 `confidence: low`。
- 用户明确确认后的页面可以标记为 `status: stable`。

## 必备维护动作

每次 ingest、query 后沉淀、lint 或重构，都需要视情况更新：

- [[40_地图与索引_Maps/LLM Wiki 总索引]]
- [[40_地图与索引_Maps/原始资料索引]]
- [[40_地图与索引_Maps/待处理队列]]
- [[40_地图与索引_Maps/维护日志]]

维护日志使用格式：

```markdown
## [YYYY-MM-DD] type | 标题
```

其中 `type` 可为：`init`、`ingest`、`query`、`lint`、`refactor`。

## 查询优先级

回答用户关于知识库的问题时：

1. 先读 [[40_地图与索引_Maps/LLM Wiki 总索引]]。
2. 再读相关 Wiki 页面。
3. 若来源或证据不足，再追溯 Raw 文件。
4. 如果回答产生有长期价值的新分析，询问或主动建议沉淀为 Wiki 页面。

## 页面格式

遵循 [[90_系统_System/LLM Wiki Schema]]。

所有长期页面应尽量包含：

- frontmatter
- 摘要或一句话定义
- 核心要点
- 详细说明
- 关联页面
- 来源
- 待澄清

## 风格

- 默认用中文维护。
- 专有名词可保留英文，并在 `aliases` 中加入英文别名。
- 少写空泛总结，多写可追溯、可链接、可行动的知识。
