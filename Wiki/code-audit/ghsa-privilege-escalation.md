---
title: GHSA — 权限提升 + 反序列化（Code Audit）
category: code-audit
tags: [ghsa, privilege-escalation, deserialization, authentication, authorization, product-index]
triggers: [提权, 权限提升, 水平越权, 垂直越权, privilege escalation, deserialization, 反序列化, 认证绕过, 鉴权缺陷, Fastjson反序列化, Apache Dubbo, Apache OfBiz, Apache Superset, Apache HertzBeat, SnakeYaml, Pickle反序列化, YAML反序列化, CraftCMS, Drupal YAML, Celery Pickle, Atlassian Confluence权限绕过, Apache CouchDB垂直越权, Airflow默认密钥, Crawlab用户添加]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/code-audit/GHSA-*]
---

## Summary

代码审计视角下的权限提升（Privilege Escalation）与反序列化（Deserialization）漏洞合集。

**总量**: code-audit/ 下约 50+ 篇 GHSA-* 文件涉及权限提升/反序列化

## Common Patterns

### 权限提升模式

| 模式 | 描述 | 典型场景 |
|------|------|---------|
| **view → edit** | 只读权限可写 | 文档/评论系统的越权编辑 |
| **user → admin** | 普通用户提权管理员 | 角色/权限判断缺失 |
| **IDOR 水平越权** | 修改 ID 访问他人数据 | `/api/user/{id}` |
| **OAuth scope 绕过** | scope 参数未校验 | OAuth 授权范围提升 |
| **SASL 用户名绕过** | 授权检查仅匹配用户名前缀 | 消息队列鉴权 |

### 反序列化模式

| 语言 | 危险类型 | Gadget 链 |
|------|---------|-----------|
| Java | `ObjectInputStream`, `readObject` | CommonsCollections, Fastjson, XStream |
| Python | `pickle.loads` | `__reduce__`, `os.system` |
| PHP | `unserialize` | `__destruct`, `__wakeup` |
| .NET | `BinaryFormatter`, `Json.NET` | `TypeNameHandling.Auto` |

## Representative GHSA Entries

| GHSA | CVE | Severity | Package | Brief |
|------|-----|----------|---------|-------|
| GHSA-3783-62vc-jr7x | — | critical (9.6) | ConsoleMe | 任意文件读→限界绕过 |
| GHSA-3989-4c6x-725f | — | critical (9.9) | XWiki Platform | view→admin 权限提升 |
| GHSA-32mf-57h2-64x9 | — | critical (10.0) | XWiki Rendering | 渲染 RCE = 任意代码执行 |
| GHSA-4g76-w3xw-2x6w | — | critical (9.1) | Kafka SASL | 用户名检查缺陷→完全绕过 |
| GHSA-5fg6-wrq4-w5gh | — | critical (9.8) | AdGuard Home | h2c 认证完全绕过 |
| GHSA-33xw-247w-6hmc | — | critical (9.8) | BentoML | pickle 反序列化 RCE |
| GHSA-3xq2-w6j4-c99r | — | critical (8.1) | Apache Seata | Java 反序列化 RCE |

## Audit Workflow

```
1. 找鉴权边界 → 识别 role/permission check
2. 找 deserialize/unserialize/loads 入口
3. 检查反序列化是否受限（白名单/类型校验）
4. 找 gadget chain 是否可达
```

## Connections

- Related: [[web/ghsa-ssrf-auth-bypass]]
- Related: [[php-deserialization-balance-tamper]]
- Related: [[mysql-udf-privilege-escalation]]
