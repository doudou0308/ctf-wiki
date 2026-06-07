---
title: InfluxDB 未授权访问漏洞
category: code-audit
tags: [influxdb, jwt, unauthorized-access, database, time-series]
triggers: [influxdb, JWT, shared-secret, 时序数据库, HS256, /debug/vars, 8086]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/code-audit/InfluxDB 未授权访问漏洞.md]
---

## Summary

InfluxDB 开启认证但未设置 `shared-secret` 参数时，JWT 认证密钥为空字符串，可伪造任意用户身份执行 SQL。

## Details

### 漏洞描述

InfluxDB 使用 JWT 鉴权。在开启了认证但未设置 `shared-secret` 时，JWT 密钥为空字符串。

### 利用方法

1. 在 jwt.io 上生成 JWT token：
   - payload: `{"username": "admin", "exp": <未来时间戳>}`
   - secret: 空字符串
   - algorithm: HS256
2. 发送带 token 的请求：
   ```
   Content-Type: application/x-www-form-urlencoded
   Authorization: Bearer <伪造的token>
   ```

### 影响版本

InfluxDB 1.x（未配置 shared-secret 的场景）

## Connections

- GHSA: [[web/ghsa-ssrf-auth-bypass]] (JWT 空密钥)
