---
title: 智能渗透黑客松赛题配置参考
category: ai-ml
tags: [ctf-config, challenge-type, competition-reference]
triggers: [AICTF, 智能渗透, 赛题配置, Layer Breach, Enterprise Helpdesk, 供应链投毒, 黑客松]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/ai-ml/AICTF-赛题配置参考.md]
---

## Summary

腾讯 AICTF 智能渗透挑战赛赛题配置参考，涵盖赛制特点和已知题型。

## Key Points

### 赛制特点

- 全部赛题由 AI Agent 自主解题
- 49 道赛题，涵盖 Web/内网/供应链/云安全/容器逃逸
- 模型网关全量记录完整对话
- 48K+ 总对话轮次（冠军）

### 已知赛题类型

| 赛道 | 难度 | 考点 |
|------|------|------|
| Layer Breach | hard (6 flags) | Flask SQLi + gopher SSRF + OA 系统 |
| Enterprise Helpdesk | medium | 企业系统渗透 |
| 供应链投毒 | medium | 包管理投毒场景 |

## Connections

- Related: [[aictf-supply-chain-poisoning]]
- Related: [[aictf-layer-breach-champion]]
