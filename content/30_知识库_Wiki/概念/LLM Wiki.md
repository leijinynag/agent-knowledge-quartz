---
type: wiki
status: generated
created: 2026-06-26
updated: 2026-06-26
aliases:
  - LLM Wiki
  - Karpathy LLM Wiki
  - 持续编译型知识库
tags:
  - llm-wiki/concept
  - knowledge-management
generated_from:
  - "[[10_原始资料_Raw/2026-06-26_用户输入_LLM-Wiki方法]]"
confidence: medium
---

# LLM Wiki

## 一句话定义

LLM Wiki 是一种让 LLM 持续维护个人知识库的方法：LLM 不只在查询时临时检索资料，而是把每次新增资料和问题沉淀为结构化、可追溯、互相链接的 Markdown Wiki。

## 核心要点

- 与普通 RAG 不同，LLM Wiki 的知识会持续累积，而不是每次提问时从零拼接。
- 原始资料保存在 Raw 层，作为不可变的事实来源。
- Wiki 层由 LLM 维护，包含概念页、人物页、主题页、对比页、综合分析等。
- Schema 层告诉 LLM 如何命名、链接、生成、更新、记录日志。
- 好的查询结果也可以回写成新页面，让探索过程继续复利。

## 三层架构

1. **Raw sources**：原始资料。对应本 vault 的 `10_原始资料_Raw/`。
2. **The wiki**：LLM 生成和维护的知识页。对应 `30_知识库_Wiki/`。
3. **The schema**：维护规范。对应 [[CLAUDE]] 和 [[90_系统_System/LLM Wiki Schema]]。

## 三种核心操作

### Ingest

当新增资料时，LLM 应读取资料、抽取要点、生成或更新 Wiki 页面，并更新索引和日志。

相关流程：[[60_工作流_Workflows/01_采集原始资料]]、[[60_工作流_Workflows/02_生成Wiki页面]]

### Query

当用户提出问题时，LLM 应先读索引，再读相关 Wiki 页面；如果证据不足，再追溯 Raw。若回答有长期价值，应沉淀为新 Wiki 页面。

相关流程：[[60_工作流_Workflows/03_查询与回答]]

### Lint

周期性检查知识库健康度：矛盾、陈旧结论、孤立页面、缺失概念、缺少来源、断链、待处理资料积压。

相关流程：[[60_工作流_Workflows/04_校验与重构]]

## 为什么有效

传统个人知识库难以长期维护，主要问题不是阅读，而是持续的 bookkeeping：更新链接、合并重复概念、标记矛盾、维护索引。LLM Wiki 把这些低价值但高频的维护动作交给 LLM，使知识库能随着资料增长而持续变好。

## 与其他概念的关系

- 对比：传统 RAG、NotebookLM、一次性文件上传问答。
- 类似精神：Vannevar Bush 的 Memex。
- 实践环境：Obsidian 作为 IDE，LLM 作为维护者，Markdown Wiki 作为 codebase。

## 来源

- [[10_原始资料_Raw/2026-06-26_用户输入_LLM-Wiki方法]]

## 待澄清

- 这个 vault 未来优先服务哪些领域：个人成长、研究、阅读、工作项目，还是综合用途？
- 是否需要启用 Dataview、Git、qmd 或其他检索工具？
- 用户希望 LLM 自动批量 ingest，还是保持“一次一篇、人在回路中”的节奏？
