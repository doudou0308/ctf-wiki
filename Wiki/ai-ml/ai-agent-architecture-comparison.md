---
title: AI 渗透 Agent 工程实践 — 架构对比
category: ai-ml
tags: [ai-agent, architecture, comparison, engineering]
triggers: [AI Agent架构, 对比, BreachWeave, Cairn, tinyctfer, AgentNote, OODA, ReAct]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/ai-ml/AICTF-AI渗透Agent架构对比.md]
---

## Summary

不同 AI 渗透 Agent 架构的优劣对比及其在 CTF 中的应用。

## Key Points

### 架构对比

| 架构 | 核心模型 | 优点 | 缺点 |
|------|---------|------|------|
| M/S/O (BreachWeave) | 三层解耦 | 全局视野+容错 | 通信开销 |
| 黑板+蚁群 (Cairn) | 平等 Worker | 灵活, 无中心瓶颈 | 收敛难 |
| 纯自然语言 FSM | Less Than Nothing | 零知识=涌现 | 稳定性 |
| 微型意图 (tinyctfer) | 100 行代码 | 极致简化 | 深度有限 |

### CTF 应用选型

- 单题快速求解: tinyctfer/AgentNote（轻量）
- 多题并行: BreachWeave（稳定）
- 深度难题: Cairn（涌现）

## Connections

- Related: [[ai-agent-architecture-patterns]]
- Related: [[ctf-agent-open-source-overview]]
