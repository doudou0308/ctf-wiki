---
title: AICTF 供应链投毒赛题分析
category: misc
tags: [supply-chain, npm, pip, package-manager, typosquatting]
triggers: [供应链, 投毒, npm, pip, typosquatting, 包管理, 私有源, 依赖注入, AICTF]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/misc/AICTF-供应链投毒.md]
---

## Summary

npm/pip 包管理器的供应链投毒攻击与检测赛题分析。

## Key Points

### 预测考点

- npm/pip 私有源污染检测
- 恶意包特征识别（混淆代码、typosquatting、依赖注入）
- 包签名/哈希校验绕过
- 依赖图分析和异常行为检测
- 运行时行为监控（网络连接/文件操作/进程创建）

### 解题策略

1. 建立包管理器监控 → 安装可疑包 → 观察行为
2. 逆向分析恶意包代码提取 C2 地址和 payload
3. 可能涉及 Docker 容器隔离分析

## Connections

- Related: [[ops-console-attack-surface]]
