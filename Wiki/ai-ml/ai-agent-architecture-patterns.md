---
title: AI 渗透 Agent 顶级架构模式
category: ai-ml
tags: [ai-agent, ctf-automation, architecture, manager-solver-observer]
triggers: [AI Agent, 架构模式, Manager-Solver-Observer, 黑板系统, 蚁群, Less Than Nothing, CTF自动化, 渗透Agent]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/ai-ml/AICTF-AI-Agent-解题架构.md]
---

## Summary

CTF 解题 AI Agent 的三种顶级架构模式：Manager-Solver-Observer 三层解耦、黑板系统+蚁群算法、Less Than Nothing 减法哲学。

## Key Points

### 模式1: Manager-Solver-Observer 三层解耦（冠军）
- Manager: 任务调度、模型路由
- Solver: 专注执行单题
- Observer: 旁路监督（不干预执行）
- 关键：RTK Rewrite 三层上下文压缩、Ralph-Loop 结束判定、7 模型并行竞争

### 模式2: 黑板系统+蚁群算法（唯一 AK）
- 平等 Worker 动态任务，反对预定义角色分工
- 涌现行为 > 人工设计流水线
- 全场唯一 AK，$7692 成本

### 模式3: Less Than Nothing
- 纯自然语言 FSM 执行引擎
- 零领域知识是主动排除而非遗漏

### 核心原则

```
1. NO_CHANGE > update > delete > add new
2. 保持耐压缩性：看板文字如代码注释般精炼
3. 先看当前 ideas 和 memory
4. 先闭环已有主线
5. 如果只是 payload 失败，记录边界不判死主线
6. 只有新证据打开不同攻击方向时才新增 idea
```

## Connections

- Related: [[ai-agent-architecture-comparison]]
- Related: [[ai-solver-rules]]
- Related: [[observer-system-prompt-analysis]]
