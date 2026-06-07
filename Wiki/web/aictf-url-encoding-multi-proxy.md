---
title: URL 编码 — 多层代理隧道中的踩坑记录
category: web
tags: [url-encoding, proxy-tunnel, gopher, debugging, encoding-pitfalls]
triggers: [URL编码, urlencode, rawurlencode, +vs%20, --data-urlencode, gfix.php, 多层代理, 编码失真]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/AICTF-URL编码多层代理踩坑.md]
---

## Summary

冠军战队 425 轮 LOG 中还原的 URL 编码实战调试记录。`curl --data-urlencode` 将空格编码为 `+` 而非 `%20`，在多层代理隧道中导致 payload 被破坏。

## Key Points

- 多层隧道中每层编码都可能变化
- `curl --data-urlencode` 在 gopher 协议下不可靠
- 调试用 hex dump 看实际传输字节，先确认传输正确性再调试 SQL payload
- 用 `rawurlencode` 确保空格为 `%20`

## Details

### 根因分析

1. `curl --data-urlencode`: 空格 → `+`（不是 `%20`）
2. `proxy.php` 转发 gopher：`+` 号在某层被解码
3. Flask POST：参数化查询 + 未回显
4. GET 请求：空格被 HTTP 请求行截断 → 400

### 解决方案

- 用 `rawurlencode` 确保空格为 `%20`
- 或用 Python 直接构造 HTTP Raw Body 绕过 Shell 转义
- 或写 PHP 脚本（gfix.php）POST raw body

## Connections

- Related: [[multi-proxy-encoding-pitfalls]]
- Related: [[ssrf-gopher-techniques]]
- Related: [[aictf-layer-breach]]
