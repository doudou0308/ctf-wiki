---
title: AICTF Layer Breach 冠军实录（425 轮完整解题链）
category: web
tags: [ssrf, gopher, docker-escape, nps-proxy, sqli, multi-layer]
triggers: [Layer Breach 冠军, 425轮, 多flag, 6 flags, Docker逃逸, ai小分队]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/AICTF-Layer-Breach-冠军实录.md]
---

## Summary

冠军战队 425 轮对话完整解题实录。多 flag（6 个）挑战，涉及 SSRF、SQL 注入、内网穿透、Docker 容器逃逸的全链路渗透。

## Key Points

### 完整解题链

1. **入口**: 10.0.160.40:80 → Nginx 反向代理
2. **SSRF**: 发现 proxy.php → gopher SSRF → 扫描内网 172.18.0.0/24
3. **Flask**: Nginx → Flask(gunicorn:172.18.0.3:8080) → 登录页
4. **Webshell**: 部署 webshell(cmd.php/sh.php) + NPC(NPS) 客户端
5. **SQL 注入**: 9 列 UNION SELECT → gfix.php 编码修复
6. **Flag**: NPS 文件浏览器 → /challenge/ → flag1.txt + flag2.txt
7. **OA 系统**: gopher SSRF → OA(SSH 后台) → 横向移动
8. **Docker**: 容器突破 → 读取容器内 flag

### 关键路径

- `/proxy.php`: gopher SSRF 入口
- `/cmd.php`: 部署的 webshell
- `/gfix.php`: rawurlencode 修复脚本
- NPS: 内网代理隧道工具
- `/challenge/flag1.txt`, `flag2.txt`: 直接命中

### 技术难点

- gopher SSRF 中空格编码问题：`+` vs `%20`
- 400 Bad Request：URL 中空格被 HTTP 请求行截断
- 多 flag 题(6 个)需逐个深入不同层次

## Connections

- Related: [[aictf-layer-breach]]
- Related: [[aictf-sqli-ssrf-tunnel]]
- Related: [[aictf-nps-proxy-tunnel]]
