---
title: AI 模型调度与分配策略（冠军实战数据）
category: ai-ml
tags: [model-routing, scheduling, cost-optimization, llm]
triggers: [模型调度, 模型分配, claude-opus, kimi-k2, glm-5, 路由策略, 成本控制, 48K轮]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/ai-ml/AICTF-AI解题模型调度策略.md]
---

## Summary

冠军战队 48K+ 对话轮次的模型分配数据与调度策略。

## Key Points

### 模型分配数据

| 模型 | 占比 | 用途 |
|------|------|------|
| claude-opus-4-6 | 39.4% | 主力解题 |
| kimi-k2.5 | 37.3% | 调度+中文上下文+纠偏 |
| glm-5.1 | 17.9% | 中文环境和代码解读 |
| gpt-5.4 | 5.2% | 特殊场景/备用 |

### 调度模式

- 调度器（kimi）：判断状态 → 分配任务
- 主 Solver（claude）：推进解题
- Observer（claude）：维护 memory/ideas + 纠偏
- 调度器不计入解题 token → 只消耗便宜 token

### 实战教训

- 模型选择 > prompt 工程
- 纠偏不及时 = 浪费数百轮对话
- 1 道 hard 题（425 轮）的对话量 = 4 道 medium 题

## Connections

- Related: [[observer-system-prompt-analysis]]
- Related: [[multi-model-context-compression]]
