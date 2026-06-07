---
title: 微信 Windows 客户端远程命令执行漏洞
category: code-audit
tags: [wechat, rce, chrome-v8, shellcode, 0day]
triggers: [微信, wechat, webchatweb.exe, 客户端RCE, Chrome V8, 0day, shellcode, CobaltStrike]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/code-audit/微信客户端 远程命令执行漏洞.md]
---

## Summary

微信 Windows PC 版 Web 组件（基于 Chrome V8 引擎）存在 RCE 漏洞，攻击者可通过钓鱼链接实现无文件落地 shellcode 执行。

## Details

### 漏洞描述

微信 Windows 版（≤3.2.1.141，截止 2022 年 12 月最新版 3.8.0.41 仍受影响）存在远程命令执行漏洞。攻击者通过微信发送恶意链接，受害者点击后 `webchatweb.exe` 会加载 shellcode 执行。

### 攻击链

1. 攻击者构造恶意钓鱼链接，通过微信发送给目标
2. 目标点击链接触发 Chrome V8 引擎漏洞
3. 无文件落地加载 CobaltStrike shellcode
4. 植入木马进程（如 `xxxsoft.exe`）并创建系统服务 `dotnet_v4.3`
5. 攻击者进一步在内网放置扫描工具（如 `TxPortMap.exe`）扫描横向

### 漏洞原理

基于 Chrome V8 引擎的 ArrayBuffer 类型混淆漏洞，通过构造 OOB（Out-of-Bounds）原语实现任意地址读写，最终执行 shellcode。

## Connections

- GHSA: [[code-audit/ghsa-rce-patterns]]
