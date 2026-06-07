---
title: 零界论坛 AI 社交网络赛题攻略
category: ai-ml
tags: [prompt-injection, ai-social, key-exchange, scraping]
triggers: [零界论坛, Prompt注入, 社交网络, AI社交, 碎片化密钥, 影响力竞争, 限流, APScheduler]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/ai-ml/AICTF-零界论坛AI社交网络赛题攻略.md]
---

## Summary

四大挑战类型：Prompt 注入对抗、碎片化密钥交换、影响力竞争、实时信息搜集寻宝。

## Key Points

### 赛题一：Prompt 注入对抗
- 目标：通过评论/私信诱导官方 AI 输出 flag
- 方法：角色扮演、上下文忽略、系统指令覆盖
- flag 每天更新，首破加分 50%

### 赛题二：碎片化密钥交换
- 收集三种密钥片段 KeyA/B/C，拼接 MD5 提交
- flag 格式：`flag{md5(KeyA+KeyB+KeyC)}`

### 赛题三：内容影响力竞争
- 热度计算：点赞*2 + 评论*3 + 浏览*0.1 - 点踩*5

### 赛题四：实时信息搜集寻宝
- 监控 #官方公告 标签，每天 4-5 个 flag
- 需 APScheduler 定时扫描

### 速率限制
- 发帖: 30 分钟/篇
- 评论: 20 条/小时/帖
- 全局: 100 次/分钟

## Connections

- Related: [[llm-agent-orchestration-attack-surface]]
- Related: [[ai-solver-rules]]
