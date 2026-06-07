---
title: CTF Agent 开源方案速查
category: ai-ml
tags: [ai-agent, open-source, ctf-automation, comparison]
triggers: [CTF Agent, 开源方案, CyberStrikeAI, BreachWeave, Cairn, tinyctfer, LuaN1aoAgent, CHYing-agent]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/ai-ml/AICTF-CTF-Agent开源方案速查.md]
---

## Summary

可复用的 CTF 解题 AI Agent 开源框架汇总，按 Stars 排序，附设计模式精华和成本参考。

## Key Points

### Top 开源方案

| 项目 | Stars | 特点 |
|------|-------|------|
| CyberStrikeAI | 4.0k | Go 构建, 100+ 安全工具 |
| BreachWeave | 276 | Manager/Observer/Solver 多角色 |
| Cairn | 1.3k | 通用状态空间搜索, 唯一 AK |
| tinyctfer | 169 | 100 行代码, 微型意图运行时 |

### 设计模式精华

- 模式1: Manager-Solver-Observer 层解耦（冠军）
- 模式2: 黑板系统+蚁群涌现（唯一 AK）
- 模式3: 纯自然语言 FSM + 零领域知识

### 成本参考

- 冠军: 7 模型并行, 48K+ 轮对话
- 唯一 AK: Cairn, $7692 API 成本
- 黑盒 81%: deadend-cli, ~$122

## Connections

- Related: [[ai-agent-architecture-comparison]]
- Related: [[ai-agent-architecture-patterns]]
