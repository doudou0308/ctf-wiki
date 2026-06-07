---
title: 多模型竞争与上下文压缩
category: ai-ml
tags: [context-management, compression, rtk-rewrite, observer, ralph-loop]
triggers: [上下文压缩, RTK Rewrite, 上下文腐败, 窗口腐败, Observer, Ralph-Loop, 纠偏]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/ai-ml/AICTF-多模型竞争与上下文压缩.md]
---

## Summary

LLM 长对话解题中的上下文腐败问题及解决方案。多轮对话后上下文过长导致模型遗忘早期线索。

## Key Points

### 解决方案（冠军队伍）

1. **RTK Rewrite 三层压缩**
   - Layer 1: 最近 N 轮原始活动日志
   - Layer 2: Observer 维护的 memory/idea 看板
   - Layer 3: 压缩摘要背景

2. **Observer 旁路模式**
   - 独立 sidecar 不参与解题
   - memory < 12 条, ideas < 8 条
   - 先闭环，后收缩，最后才扩张

3. **Ralph-Loop 结束判定**
   - 系统状态约束判断是否该结束当前策略

### 误区

- Agent 反复尝试已确认失败的方向
- 发现高价值新通道后回退到低价值行为
- 不记录失败边界导致重复试错

## Connections

- Related: [[observer-system-prompt-analysis]]
- Related: [[model-scheduling-strategy]]
- Related: [[ai-solver-rules]]
