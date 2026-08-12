---
type: wiki
status: draft
created: 2026-06-26
updated: 2026-06-26
aliases:
  - Canvas Ray Casting
  - Canvas命中检测
  - Ray Casting
tags:
  - frontend
  - canvas
  - interview
generated_from:
  - "用户提供的简历截图"
confidence: medium
---

# Canvas命中检测与RayCasting

## 一句话定义

Canvas 命中检测是判断鼠标点击或拖拽位置是否落在某个图形内部的技术；Ray Casting 常用于判断一个点是否在多边形内部。

## 核心思路

Ray Casting 判断点是否在多边形内：

1. 从目标点向一个方向发射射线。
2. 统计射线与多边形边的交点数量。
3. 交点数为奇数，点在多边形内；偶数则在外。

## 前端实现要点

- 服务端返回多边形点集。
- 前端把图像坐标、Canvas 坐标、缩放比例统一。
- 鼠标事件坐标转换成 Canvas 坐标。
- 使用 Ray Casting 判断命中的区域。
- 命中后更新选区、样式和交互状态。

## 面试回答模板

> 这个功能的难点不是单纯画 Canvas，而是坐标系统和命中检测。我会先统一服务端图像坐标与前端 Canvas 坐标，再对用户点击点做 Ray Casting 判断是否落在服务端返回的多边形内，这样用户可以直接点选智能区域，而不需要手动描边。

## 高频追问

- Canvas 坐标和图片原始坐标如何转换？
- 多边形边界点怎么处理？
- 如果选区很多，性能如何优化？
- 为什么不用 DOM 元素覆盖？

## 相关页面

- [[30_知识库_Wiki/主题/前端面试/前端场景题]]
- [[30_知识库_Wiki/主题/前端面试/前端性能优化]]
