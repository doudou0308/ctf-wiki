---
title: AI Agent 解题强制规则（从冠军 Observer 系统提炼）
category: ai-ml
tags: [ai-agent, rules, observer, solver, efficiency]
triggers: [强制规则, AI解题, 元规则, 零轮侦查, 1轮失败, 300次爆破, 纠偏, Observer]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/ai-ml/AICTF-AI解题强制规则.md]
---

## Summary

AI Agent 解 CTF 题时必须遵守的 10 条元规则，从冠军团队策略中提炼。

## Key Points

### 核心规则清单

```
1. flag 到手立即停
2. 零轮侦查: strings|grep flag + file 并行跑
3. 1 轮失败→反思分类; 2 轮失败→必换策略
4. 发现高价值新通道后禁止回退到低价值行为
5. 失败边界记录优于直接判死整条主线
6. 密码爆破 > 300 次 = 立即换方向（硬规则）
7. 维护紧凑看板: memory<12, ideas<8
8. 先闭环已有主线，后扩张新方向
9. URL 编码失真是最容易被忽略的失败原因
10. 多层代理隧道中每层编码都可能变化
```

### Observer 设计原则

- 旁路不干预执行
- `NO_CHANGE > update > delete > add new`
- 只保留 durable facts / evidence / failure boundaries / hints

### 低效行为识别（必须打断）

- 手工逐个 fuzz / 列目录
- 重复低增量试错（密码爆破 >300 次）
- 回退到已被证明失败的方向

## Connections

- Related: [[observer-system-prompt-analysis]]
- Related: [[multi-model-context-compression]]
- Related: [[llm-agent-orchestration-attack-surface]]
