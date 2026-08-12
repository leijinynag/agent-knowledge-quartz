---
type: schema
status: stable
created: 2026-06-26
updated: 2026-06-26
aliases:
  - LLM Wiki Schema
  - 知识库维护规范
tags:
  - llm-wiki/system
---

# LLM Wiki Schema

本文件定义这个 vault 的 LLM Wiki 结构、frontmatter、页面类型和维护规则。

## Frontmatter 标准

```yaml
---
type: source | wiki | moc | workflow | template | log | staging | home | schema
status: raw | draft | generated | needs_review | stable | deprecated | active
created: YYYY-MM-DD
updated: YYYY-MM-DD
aliases: []
tags: []
source_id:
generated_from: []
confidence: low | medium | high
---
```

## 页面类型

| type | 目录 | 用途 |
|---|---|---|
| `source` | `10_原始资料_Raw/` | 原始资料、剪藏、用户粘贴内容、会议记录 |
| `staging` | `20_处理暂存_Staging/` | 摘要、抽取结果、待确认中间产物 |
| `wiki` | `30_知识库_Wiki/` | 长期维护的概念、人物、项目、主题、问题页 |
| `moc` | `40_地图与索引_Maps/` | Map of Content、索引、导航 |
| `workflow` | `60_工作流_Workflows/` | 操作流程 |
| `template` | `50_模板_Templates/` | 可复用页面模板 |
| `log` | `40_地图与索引_Maps/` | 维护日志 |
| `home` | `00_入口/` 或根入口 | 首页、工作台 |
| `schema` | `90_系统_System/` | 系统规范 |

## 状态

| status | 含义 |
|---|---|
| `raw` | 原始资料，未加工或仅加元信息 |
| `draft` | 草稿，尚未稳定 |
| `generated` | LLM 已生成，等待后续使用或确认 |
| `needs_review` | 需要人工确认或进一步查证 |
| `stable` | 已确认，可作为较稳定参考 |
| `deprecated` | 已废弃或被新页面替代 |
| `active` | 当前使用中的入口或流程 |

## Wiki 页面必备结构

长期 Wiki 页面建议包含：

```markdown
# 标题

## 一句话定义

## 核心要点

## 详细说明

## 与其他概念的关系

## 来源

## 待澄清
```

## 可追溯性规则

- `type: wiki` 页面必须设置 `generated_from`。
- `generated_from` 应使用 wiki-links，例如：

```yaml
generated_from:
  - "[[10_原始资料_Raw/2026-06-26_用户输入_LLM-Wiki方法]]"
```

- 对来源不足的结论，使用：

```yaml
status: needs_review
confidence: low
```

## 命名规范

- Raw：`YYYY-MM-DD_来源_短标题.md`
- Wiki 概念页：`概念名.md`
- MOC：`主题名 MOC.md`
- 工作流：`NN_动作名称.md`

## 维护日志格式

每个日志条目以统一二级标题开头，便于检索：

```markdown
## [YYYY-MM-DD] type | 标题
```

示例：

```markdown
## [2026-06-26] init | 初始化 LLM Wiki 骨架
```

## LLM 维护检查清单

每次修改后检查：

- [ ] 是否更新了相关索引？
- [ ] 是否更新了 [[40_地图与索引_Maps/维护日志]]？
- [ ] 新 Wiki 页面是否有 `generated_from`？
- [ ] 重要概念是否互相链接？
- [ ] 不确定内容是否标记为 `needs_review`？
