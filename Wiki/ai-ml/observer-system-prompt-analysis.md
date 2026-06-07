---
title: Observer 系统提示词完整分析（冠军开源精华）
category: ai-ml
tags: [observer, system-prompt, ai-agent, memory-management, correction]
triggers: [Observer, 系统提示词, 看板, Ideas, Memory, 纠偏, 旁路模式, RTK Rewrite]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/ai-ml/AICTF-Observer系统提示词分析.md]
---

## Summary

AI CTF 解题系统中 Observer/Solver 分离的工程实践。Observer 旁路维护 compact 看板，不参与解题。

## Key Points

### Observer 核心职责

- 维护两块板：Ideas 板（待验证方向）+ Memory 板（已知事实/边界）
- Ideas < 8 条, Memory < 12 条 → 强制体积控制
- `NO_CHANGE > update > delete > add new`
- 先闭环、后收缩、最后才扩张

### Ideas 管理原则

- 具体+可执行+可验证才算有效 idea
- 同一主线不拆成多条重复 idea
- verified/failed 必须含决定性证据摘要

### Memory 管理原则

- 合并重于累加（新证据改写旧记录）
- failure memory 整理成边界结论（非动作流水）
- 环境限制（无外网/只读FS/沙箱）高优先级

### 纠偏机制

- `send_efficiency_reminder` 是最后手段（4 个前提必须满足）
- 典型低效：手工 fuzz、逐目录扫描、>300 次爆破
- 实战效果：425 轮 Layer Breach → 4 次纠偏成功阻止低效循环

## Connections

- Related: [[ai-solver-rules]]
- Related: [[model-scheduling-strategy]]
- Related: [[multi-model-context-compression]]
