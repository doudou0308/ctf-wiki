---
title: AICTF — Enterprise Helpdesk 企业系统渗透
category: web
tags: [enterprise, helpdesk, privilege-escalation, file-upload]
triggers: [helpdesk, ticket, 企业系统, 工单系统, 权限提升, 越权]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/AICTF-Enterprise-Helpdesk.md]
---

## Summary

企业 Helpdesk 工单系统的权限提升与数据泄露，涉及水平越权、垂直越权、文件上传等攻击面。

## Key Points

### 通用解题策略

1. 枚举 Helpdesk 系统功能（ticket 创建/查看/回复/附件）
2. 测试水平越权（查看其他用户 ticket）
3. 测试垂直越权（普通用户 → 管理员功能）
4. 搜索接口注入
5. 文件上传 → webshell

## Connections

- Related: [[enterprise-document-report-attack-surface]]
- Related: [[ops-console-attack-surface]]
