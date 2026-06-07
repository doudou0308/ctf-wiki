---
title: Aria2 任意文件写入漏洞
category: code-audit
tags: [aria2, rpc, cron, file-write, arbitrary-file]
triggers: [aria2, 6800, jsonrpc, xmlrpc, RPC接口, Vulhub, crontab, 定时任务]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/code-audit/Aria2 任意文件写入漏洞.md]
---

## Summary

Aria2 的 JSON-RPC/XML-RPC 接口在有权限时可写入任意文件。通过 RPC 接口将恶意文件下载至 `/etc/cron.d/` 实现计划任务反弹 shell。

## Details

### 漏洞描述

Aria2 是一个命令行下轻量级、多协议、多来源的下载工具，内建 XML-RPC 和 JSON-RPC 接口。默认监听 6800 端口。

### 利用流程

1. 借助第三方 UI（如 yaaw）与 RPC 服务通信
2. 配置目标域名 `http://your-ip:6800/jsonrpc`
3. 添加下载任务，Dir 填写 `/etc/cron.d/`，File Name 填写 shell
4. 在 VPS 上准备反弹 shell 脚本
5. 等待 cron 执行（≤1 分钟）

### 关键注意

- crontab 文件格式需满足：换行符必须是 `\n`，文件结尾需要有一个换行符
- `/etc/cron.d/` 下所有文件将被作为计划任务配置文件读取
- debian/ubuntu 可用此路径，部分发行版可能不同

## Connections

- GHSA: [[code-audit/ghsa-rce-patterns]]
- Related: [[docker-container-pentest]]
