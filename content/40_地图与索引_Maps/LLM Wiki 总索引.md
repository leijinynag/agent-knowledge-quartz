---
type: moc
status: active
created: 2026-06-26
updated: 2026-07-11
aliases:
  - index.md
  - 总索引
  - Knowledge Base Index
tags:
  - llm-wiki/index
---

# LLM Wiki 总索引

这是知识库的内容索引。回答问题时，LLM 应先阅读本页，再进入相关页面。

## 入口

- [[欢迎]]：vault 总入口
- [[00_入口/首页]]：日常工作台
- [[40_地图与索引_Maps/面试准备总览 MOC]]：前端 / Agent 开发面试准备入口

## 系统与规范

- [[CLAUDE]]：LLM 维护规约
- [[90_系统_System/LLM Wiki Schema]]：页面类型、frontmatter、命名规范

## 面试准备

| 页面 | 摘要 | 状态 |
|---|---|---|
| [[40_地图与索引_Maps/面试准备总览 MOC]] | 前端 / Agent 开发面试准备总入口。 | active |
| [[40_地图与索引_Maps/前端面试 MOC]] | 前端八股、手撕代码、场景题、性能优化、工程化入口。 | active |
| [[40_地图与索引_Maps/Agent开发面试 MOC]] | LLM 应用、Tool Use、MCP、Agent 系统设计入口。 | active |
| [[40_地图与索引_Maps/项目经历 MOC]] | 个人项目和面试讲述模板。 | active |
| [[40_地图与索引_Maps/面试复盘日志]] | mock / 真实面试复盘日志。 | active |
| [[30_知识库_Wiki/主题/面试/简历针对性学习路线]] | 根据简历项目和目标岗位生成的学习路线。 | generated |

## 项目经历

| 页面 | 摘要 | 状态 |
|---|---|---|
| [[30_知识库_Wiki/项目/个人项目/简历项目雷达]] | 从简历提取的项目能力雷达和学习方向。 | generated |
| [[30_知识库_Wiki/项目/个人项目/Velaire]] | Bun + TypeScript 通用 Agent Runtime / Claude Code 类项目。 | generated |
| [[30_知识库_Wiki/项目/个人项目/在线教学管理系统]] | Vue3 + Pinia + Socket.io + JWT 在线教学管理系统。 | draft |

## 核心概念

| 页面 | 摘要 | 来源数 | 状态 |
|---|---|---:|---|
| [[30_知识库_Wiki/概念/LLM Wiki]] | 用 LLM 持续维护可追溯、互相链接、会累积的 Markdown Wiki。 | 1 | generated |

## 前端与 JavaScript

| 页面 | 摘要 | 来源数 | 状态 |
|---|---|---:|---|
| [[30_知识库_Wiki/主题/前端手写代码]] | 前端面试常见 JS/TS 手写题的主题入口，覆盖函数工具、原型机制、数据结构处理和异步控制。 | 1 | generated |
| [[30_知识库_Wiki/主题/前端面试/前端八股总览]] | HTML、CSS、JS、Vue、React、网络等前端基础面试总览。 | 0 | draft |
| [[30_知识库_Wiki/主题/前端面试/网络面试]] | HTTP、缓存、跨域、WebSocket/SSE 等网络面试入口。 | 0 | draft |
| [[30_知识库_Wiki/概念/Agent开发/SSE流式Agent状态同步]] | SSE 协议、WebSocket 对比、前后端实现、打字机效果、边界和性能优化。 | 2 | generated |
| [[30_知识库_Wiki/主题/前端面试/JavaScript面试]] | JavaScript 概念类与手写类面试题入口。 | 0 | draft |
| [[30_知识库_Wiki/概念/JavaScript/JavaScript概念面试题索引]] | JS 概念类面试题索引。 | 0 | active |
| [[30_知识库_Wiki/概念/JavaScript/数据类型与类型判断]] | JS 数据类型、typeof、instanceof、toString 类型判断。 | 0 | draft |
| [[30_知识库_Wiki/概念/JavaScript/原型链与继承]] | prototype、原型链、class 继承基础。 | 0 | draft |
| [[30_知识库_Wiki/概念/JavaScript/JavaScript继承方式]] | 原型链继承、构造函数继承、组合继承、寄生组合继承和 class 继承的优缺点。 | 0 | draft |
| [[30_知识库_Wiki/概念/JavaScript/闭包]] | 闭包、词法作用域、状态保存和内存风险。 | 0 | draft |
| [[30_知识库_Wiki/概念/JavaScript/this绑定规则]] | 默认、隐式、显式、new 和箭头函数 this。 | 0 | draft |
| [[30_知识库_Wiki/概念/JavaScript/执行上下文与作用域链]] | 执行上下文、变量提升、TDZ 和作用域链。 | 0 | draft |
| [[30_知识库_Wiki/概念/JavaScript/事件循环与宏任务微任务]] | Event Loop、宏任务、微任务和执行顺序。 | 0 | draft |
| [[30_知识库_Wiki/概念/JavaScript/Promise与async-await]] | Promise 状态、链式调用、async/await 和错误处理。 | 0 | draft |
| [[30_知识库_Wiki/概念/JavaScript/手写简易版Promise]] | 简易 Promise 状态机、回调队列和 then 链式调用实现。 | 0 | generated |
| [[30_知识库_Wiki/概念/JavaScript/手写Promise.all]] | Promise.all 的计数器、结果顺序和 fail-fast 实现。 | 0 | generated |
| [[30_知识库_Wiki/概念/JavaScript/手写发布订阅模式]] | EventEmitter / 发布订阅模式的 on、off、emit、once 实现。 | 0 | generated |
| [[30_知识库_Wiki/概念/JavaScript/模块化]] | CommonJS、ES Module、live binding 和 tree-shaking。 | 0 | draft |
| [[30_知识库_Wiki/概念/JavaScript/垃圾回收与内存泄漏]] | GC、标记清除、常见内存泄漏与排查。 | 0 | draft |
| [[30_知识库_Wiki/概念/JavaScript/浅拷贝与深拷贝]] | 浅拷贝、深拷贝、JSON 方法和 structuredClone。 | 0 | draft |
| [[30_知识库_Wiki/概念/JavaScript/事件委托]] | 事件冒泡、target/currentTarget 和委托场景。 | 0 | draft |
| [[30_知识库_Wiki/概念/JavaScript/Map与WeakMap]] | Map、WeakMap、弱引用和内存管理。 | 0 | draft |
| [[30_知识库_Wiki/主题/前端面试/手撕代码与算法]] | 手写实现与算法题复习入口。 | 0 | draft |
| [[30_知识库_Wiki/主题/前端面试/前端场景题]] | 前端排查、设计、取舍类开放题入口。 | 0 | draft |
| [[30_知识库_Wiki/主题/前端面试/前端性能优化]] | 首屏、白屏、包体积、渲染和监控优化。 | 0 | draft |
| [[30_知识库_Wiki/主题/前端面试/前端工程化]] | 构建、CI/CD、规范、测试、发布和监控。 | 0 | draft |
| [[30_知识库_Wiki/概念/前端工程化/微前端与React子应用]] | 微前端集成 Vue 主应用与 React 子应用。 | 0 | draft |
| [[30_知识库_Wiki/概念/前端工程化/iframe与postMessage通信]] | iframe 双向通信协议、origin 校验与状态恢复。 | 0 | draft |
| [[30_知识库_Wiki/概念/前端工程化/Canvas命中检测与RayCasting]] | Canvas 多边形选区命中检测与 Ray Casting。 | 0 | draft |
| [[30_知识库_Wiki/概念/前端工程化/WebSocket在线状态与实时通信]] | Socket.io / WebSocket 在线状态、心跳和重连。 | 0 | draft |
| [[30_知识库_Wiki/概念/前端工程化/JWT与前端权限管理]] | JWT、路由守卫、动态菜单和权限控制。 | 0 | draft |
| [[30_知识库_Wiki/概念/函数柯里化]] | 将多参数函数转换为可分步接收参数的函数。 | 1 | generated |
| [[30_知识库_Wiki/概念/once 函数]] | 确保函数只执行一次，后续调用返回首次结果。 | 1 | generated |
| [[30_知识库_Wiki/概念/防抖与节流]] | 控制高频事件触发频率的两类函数包装模式。 | 1 | generated |
| [[30_知识库_Wiki/概念/手写 new]] | 模拟 new 运算符的对象创建、原型连接、构造函数调用和返回值处理。 | 1 | generated |
| [[30_知识库_Wiki/概念/手写 instanceof]] | 沿对象原型链查找构造函数 prototype 的 instanceof 模拟实现。 | 1 | needs_review |
| [[30_知识库_Wiki/概念/JavaScript 运算符优先级与原型题]] | 解释 Foo.getName 经典题中的声明提升、原型方法、静态方法和 new 运算符优先级。 | 1 | generated |
| [[30_知识库_Wiki/概念/数组扁平化]] | 递归展开嵌套数组为一维数组。 | 1 | generated |
| [[30_知识库_Wiki/概念/深拷贝]] | 递归复制对象和数组，并用 WeakMap 处理循环引用。 | 1 | needs_review |
| [[30_知识库_Wiki/概念/Promise 并发控制]] | 用固定数量 worker 执行异步任务队列并保持结果顺序。 | 1 | generated |
| [[30_知识库_Wiki/概念/Fetch 超时与重试]] | 基于 AbortController、重试次数和延迟封装 fetch 请求。 | 1 | generated |

## Agent 开发

| 页面 | 摘要 | 状态 |
|---|---|---|
| [[30_知识库_Wiki/主题/Agent开发/Agent开发路线]] | Agent 开发面试学习路线。 | draft |
| [[30_知识库_Wiki/主题/Agent开发/LLM应用开发]] | LLM 应用基础能力入口。 | draft |
| [[30_知识库_Wiki/主题/Agent开发/Agent系统设计]] | Agent 架构、工具、上下文、评估、安全设计。 | draft |
| [[30_知识库_Wiki/主题/Agent开发/Tool Use与函数调用]] | Tool schema、tool result loop、权限与错误处理。 | draft |
| [[30_知识库_Wiki/主题/Agent开发/MCP与工具生态]] | MCP 和外部工具生态接入。 | draft |
| [[30_知识库_Wiki/主题/Agent开发/Agent记忆与上下文管理]] | memory、compaction、prompt caching、context 管理。 | draft |
| [[30_知识库_Wiki/主题/Agent开发/Agent评估与可观测性]] | eval、trace、日志、回放和结果评估。 | draft |
| [[30_知识库_Wiki/主题/Agent开发/Agent安全与权限]] | prompt injection、secret、权限、human-in-the-loop。 | draft |
| [[30_知识库_Wiki/概念/Agent开发/事件驱动Agent Runtime]] | 事件驱动 Agent Runtime 与 UI/log/replay 复用。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Provider Adapter模型抽象]] | Provider 无关模型适配层。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Capability-based Tool Policy]] | 基于 capability 的工具权限策略。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent Skills Progressive Loading]] | Skills 渐进式加载与上下文管理。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/多Agent编码工作流]] | Planner / Generator / Evaluator 多 Agent 编码流程。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent可观测性与RuntimeEvent]] | RuntimeEvent、日志、timeline 和回放。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/SSE流式Agent状态同步]] | Agent SSE 事件流、前后端实现、断线恢复和性能优化。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent系统组件边界]] | Agent、RAG、Tool、Skill、MCP 与 Runtime 的职责边界。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agentic RAG检索决策与质量门控]] | 选择性检索、多知识库路由、证据门控和受限恢复。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent可观测性三支柱与轨迹调试]] | Metrics、Tracing、结构化日志和 Agent 轨迹排障。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/前端流式Agent交互]] | POST SSE、字节流解析、前端状态机和流式渲染性能。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent规划范式与动态重规划]] | ReAct、Plan-and-Execute、阶段化流程和重规划触发条件。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent工具执行的幂等重试与超时]] | 工具幂等、错误分类、deadline、重试和取消传播。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent长期记忆写入与遗忘策略]] | 长期记忆的写入门槛、scope、冲突处理、TTL 和用户控制。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent风险分级与人工审批]] | 工具风险分级、具体动作审批和 approval token 设计。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent提示注入与不可信内容隔离]] | 直接/间接提示注入、指令数据隔离和系统层防护。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent持久化执行与断点恢复]] | 长任务 checkpoint、事件日志、worker 接管和副作用恢复。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent对话UI状态机]] | Agent 对话中的 run 状态机、事件归并和 UI 投影。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent前端输入框与快捷指令设计]] | Agent 输入框作为任务提交器，管理上下文引用、快捷指令、模式和发送前校验。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent工具调用确认面板]] | 工具审批面板的信息展示、交互状态和后端校验。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent工具结果展示与折叠策略]] | 工具结果的摘要层、结果层、原始层展示和不同工具的可读 UI。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent多轮任务的进度表达]] | Agent 多轮任务用阶段、当前动作、工具状态和等待原因表达进度。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/流式Markdown渲染与代码块中间态]] | LLM 流式输出里的 Markdown 中间态、代码块补全和渲染节流。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent错误恢复与用户可理解反馈]] | 网络、模型、工具、权限等错误分类与前端恢复动作。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent会话历史与前端本地缓存]] | Agent 会话的本地缓存边界、刷新恢复和隐私控制。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent测试用例生成与回归集沉淀]] | Agent 回归集沉淀、轨迹评估、工具行为约束和失败案例复用。 | generated |
| [[30_知识库_Wiki/概念/Agent开发/Agent成本预算与Token治理]] | Agent Runtime 中的 token、循环轮数、工具调用和上下文预算治理。 | generated |

## 原始资料

- [[40_地图与索引_Maps/原始资料索引]]

## 工作流

- [[60_工作流_Workflows/01_采集原始资料]]
- [[60_工作流_Workflows/02_生成Wiki页面]]
- [[60_工作流_Workflows/03_查询与回答]]
- [[60_工作流_Workflows/04_校验与重构]]

## 维护

- [[40_地图与索引_Maps/待处理队列]]
- [[40_地图与索引_Maps/维护日志]]

## 未来可扩展分类

- 人物：`30_知识库_Wiki/人物/`
- 项目：`30_知识库_Wiki/项目/`
- 主题：`30_知识库_Wiki/主题/`
- 问题：`30_知识库_Wiki/问题/`
