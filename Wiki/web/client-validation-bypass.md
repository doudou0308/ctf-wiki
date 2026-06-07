---
title: 客户端校验绕过 — 游戏分数篡改
category: web
tags: [client-side, bypass, burpsuite, frontend-security]
triggers: [客户端校验, 前端校验, 分数修改, Burp Suite, 抓包改包, 前端安全, game score]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/客户端校验绕过.md]
---

## Summary

游戏分数在客户端 JS 判断，拦截 HTTP 请求修改分数参数即可绕过。

## Key Points

- 任何在客户端做的校验都可以绕过
- 前端只控制 UI，后端才做安全检查
- 关键在于找到分数的请求参数位置

## Details

### 攻击流程

1. 打开 Burp Suite 开启拦截
2. 玩游戏触发分数提交请求
3. 将请求体中的分数参数改为目标值（如 400）
4. Forward 放行
5. 响应返回 flag

## Connections

- GHSA: [[web/ghsa-xss-csrf]]
