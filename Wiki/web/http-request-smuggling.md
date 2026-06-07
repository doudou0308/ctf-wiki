---
title: HTTP 请求走私 — CL/TE 双重响应利用
category: web
tags: [http-request-smuggling, nginx, ssrf, bypass]
triggers: [请求走私, request smuggling, CL/TE, Transfer-Encoding, proxy.php, nginx bypass, 双重响应]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/AICTF-HTTP请求走私.md]
---

## Summary

利用 Nginx 与后端服务器对 Content-Length 和 Transfer-Encoding 的解析差异，触发双重响应绕过前端访问控制。

## Key Points

- CL/TE 解析差异是常见走私入口
- Nginx 在前端时后端可能收到两个请求
- 走私成功后可访问前端禁止的路径（如 /init.sql, /admin, 内部API）
- 发现走私后应立即深入挖掘后端隐藏端点，不要回退到已失败的爆破方向

## Details

### 触发条件

- Nginx 处理 CL，后端处理 TE（或反之）
- 构造畸形的 HTTP 请求使两个服务器解析出不同边界
- 第一个请求"污染"下一个请求的起始

### 利用流程

1. 发送精心构造的 CL/TE 冲突请求
2. 观察响应 — 双重响应（如 R319+R320）表明走私成功
3. 通过走私请求访问前端禁止的端点如 `/init.sql`、`/admin`、内部 API

## Connections

- Related: [[ssrf-gopher-techniques]]
- Related: [[multi-proxy-encoding-pitfalls]]
- Related: [[aictf-sqli-ssrf-tunnel]]
- GHSA: [[web/ghsa-ssrf-auth-bypass]]
