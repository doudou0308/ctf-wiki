---
title: 多语言 SQL 注入 — SSRF 代理编码陷阱
category: web
tags: [sqli, url-encoding, proxy-tunnel, gopher, http-raw]
triggers: [编码陷阱, SQL注入编码, 多层代理, 编码失真, +号转码, HTTP原始请求, gopher, rawurlencode]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/AICTF-多层代理编码陷阱.md]
---

## Summary

多层代理场景下的 HTTP 参数编码失真问题。每个中间层都可能改变编码，导致本地测试成功的 payload 通过隧道后失败。

## Key Points

- 多层代理/隧道场景下的编码问题是最容易被忽略的失败原因
- 每个中间层（`curl → proxy.php → gopher → Flask`）都可能改变编码
- 当 payload 在本地测试成功但通过隧道失败时，先怀疑编码失真

## Details

### 核心踩坑

`curl --data-urlencode` 会将空格编码为 `+` 而非 `%20`。通过 `proxy.php → gopher → Flask` 时 `+` 号在某层被解码成空格，导致 SQLi payload 错位。

### 正确做法

```bash
# 错误方式（+号会被中间层转码）
curl "http://proxy/?url=gopher://target/_POST%20/login%20..."

# 正确方式：用 rawurlencode 确保 %20 不变成 +
# 或直接写脚本构造原始 HTTP Body
```

## Connections

- Related: [[aictf-url-encoding-multi-proxy]]
- Related: [[ssrf-gopher-techniques]]
- Related: [[http-request-smuggling]]
