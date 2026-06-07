---
title: GHSA — SSRF + 认证绕过 + JWT/OAuth（Web 安全）
category: web
tags: [ghsa, ssrf, auth-bypass, jwt, oauth, oidc, authentication]
triggers: [SSRF, 认证绕过, 鉴权绕过, JWT, OAuth, OIDC, PKCE, authentication bypass, unauthorized, privilege escalation, 未授权]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/GHSA-*]
---

## Summary

SSRF（服务端请求伪造）、认证绕过（Authentication Bypass）、JWT/OAuth 鉴权缺陷合集。

**总量**: web/ 下约 60+ 篇 GHSA-* 文件涉及 SSRF/认证/JWT/OAuth

## Common Patterns

### SSRF 入口模式

| 模式 | 描述 | 常见位置 |
|------|------|---------|
| **数据源配置** | 创建/修改数据源 URL | Grafana, 监控平台 |
| **文件下载/预览** | 远程资源抓取 | 文档预览, 图片处理 |
| **Webhook/Callback** | URL 回调 | CI/CD, Webhook |
| **LFS 端点** | Git LFS 远程拉取 | Gitea, GitHub 替代品 |
| **代理模式** | 用户可控的代理地址 | proxy.php, gopher |

### 认证绕过模式

| 模式 | 描述 |
|------|------|
| **Header 信任** | `X-Forwarded-For` / `X-Admin-Token` 身份信任 |
| **默认凭证** | admin/admin, root/root 等默认口令 |
| **空密钥 JWT** | `shared-secret` 未配置, JWT 密钥为空 |
| **SASL 绕过** | 授权用户名比较缺陷 |
| **h2c 升级** | HTTP/2 Cleartext 绕过 TLS 认证 |
| **Liferay OAuth XSS** | OAuth 流程中的注入→身份冒充 |

### JWT/OAuth 模式

- JWT alg=none 绕过
- JWT 空密钥/弱密钥
- OAuth redirect_uri 未校验
- PKCE code_verifier 生成弱随机
- State parameter 随机性不足

## Representative GHSA Entries

| GHSA | CVE | Severity | Package | Brief |
|------|-----|----------|---------|-------|
| GHSA-3fvx-xrxq-8jvv | — | critical (9.1) | soft-serve | LFS 端点 SSRF |
| GHSA-3q6g-qmpx-rqw4 | — | critical (9.1) | Whoogle | SSRF |
| GHSA-4g76-w3xw-2x6w | — | critical (9.1) | Kafka | SASL 认证完全绕过 |
| GHSA-5fg6-wrq4-w5gh | — | critical (9.8) | AdGuard Home | h2c 认证绕过 |
| GHSA-6f9g-cxwr-q5jr | — | critical (9.8) | Jenkins CLI | 任意文件读→凭证窃取 |
| GHSA-7ppg-37fh-vcr6 | — | critical (9.8) | Milvus | 未授权访问监控端口 |
| GHSA-2m7h-86qq-fp4v | CVE-2022-31034 | high (8.4) | Argo CD | PKCE/OAuth 弱随机参数 |
| GHSA-3xq5-x4fj-rff7 | — | critical (9.1) | DB-GPT | 路径遍历 + 认证绕过 |

## Connections

- Related: [[ssrf-gopher-techniques]]
- Related: [[grafana-ssrf]]
- Related: [[influxdb-unauthorized-access]]
- Related: [[aictf-layer-breach]]
