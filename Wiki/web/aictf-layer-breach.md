---
title: AICTF Layer Breach — SSRF 多层内网渗透
category: web
tags: [ssrf, gopher, sqli, flask, multi-layer, nps-proxy]
triggers: [Layer Breach, AICTF, gopher SSRF, 多层渗透, 内网穿透, Flask SQL注入, 172.18.0.0/24]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/AICTF-Layer-Breach.md]
---

## Summary

通过 gopher 协议 SSRF 绕过前端代理，逐层穿透内网，对内部 Flask 服务做 SQL 注入，结合 NPS 代理实现多层网络渗透。

## Key Points

- gopher:// 协议可构造任意 TCP 数据流，绕过 HTTP 代理限制
- 多层渗透中先用 SSRF 打通内网，再在内网目标上做 SQL 注入
- 不要陷入密码爆破死循环（>300 次尝试 = 明确该换方向）
- URL 编码陷阱：通过代理/隧道发送时 `+` 号会被转成空格

## Details

### 攻击链

1. 入口：Nginx 反向代理 → 发现 proxy.php → gopher SSRF
2. 扫描内网 172.18.0.0/24 → 发现 Flask(gunicorn:172.18.0.3:8080)
3. 部署 webshell + NPS 客户端建立隧道
4. Flask SQL 注入：9 列 UNION SELECT
5. NPS 文件浏览器直接读取 flag 文件

### 核心命令

```bash
# gopher SSRF 绕过 URL 编码问题
curl "http://target/gfix.php?h=10.0.160.40&port=80&p=/login" \
  --data-urlencode "c=echo 'POST /login' | nc target 80"
```

## Connections

- Related: [[ssrf-gopher-techniques]]
- Related: [[aictf-sqli-ssrf-tunnel]]
- Related: [[aictf-layer-breach-champion]]
- Related: [[multi-proxy-encoding-pitfalls]]
- Related: [[aictf-nps-proxy-tunnel]]
