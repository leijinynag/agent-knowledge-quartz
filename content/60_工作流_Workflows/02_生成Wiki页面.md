---
type: workflow
status: active
created: 2026-06-26
updated: 2026-06-26
aliases:
  - Generate Wiki Pages
tags:
  - llm-wiki/workflow
---

# 02_生成Wiki页面

## 何时使用

当 Raw source 已采集，需要把资料编译进长期 Wiki 时使用。

## 输入

- 一个或多个 Raw source 页面。
- 可选：用户指定的关注点、分类、输出格式。

## 步骤

1. 阅读相关 Raw source。
2. 抽取核心概念、人物、项目、主题、问题。
3. 判断是否需要新建页面，或更新已有 Wiki 页面。
4. 对每个 Wiki 页面添加或更新 `generated_from`。
5. 补充交叉链接：相关概念、上位/下位概念、对比概念。
6. 若存在矛盾或不确定结论，标记 `needs_review` 或 `confidence: low`。
7. 更新 [[40_地图与索引_Maps/LLM Wiki 总索引]]。
8. 更新 [[40_地图与索引_Maps/维护日志]]。

## 输出

- 新增或更新的 Wiki 页面。
- 更新后的总索引和维护日志。
- 可选：待澄清问题加入 [[40_地图与索引_Maps/待处理队列]]。

## 质量标准

- 每个结论尽量能追溯到来源。
- 页面之间应该形成链接，而不是孤立摘要。
- 不要为了创建页面而创建页面；优先创建未来会被重复引用的概念。
