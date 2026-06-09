---
title: "2026 长城杯 AWDP 半决赛 WP (西南地区 WEB)"
category: ctf
tags: [ctf, 长城杯, awdp, 半决赛, web]
triggers: [长城杯AWDP, 长城杯半决赛, 2026长城杯]
created: 2026-06-09
updated: 2026-06-09
sources: [raw/2026长城杯AWDP半决赛WP(西南地区WEB) - Rycarls little blog.md]
---

# 2026 长城杯 AWDP 半决赛 WP (西南地区 WEB)

> 作者：Rycarl | 来源：[Rycarl's Blog](https://blog.rycarl.cn/index.php/2026/03/23/2026%e9%95%bf%e5%9f%8e%e6%9d%afawdp%e5%8d%8a%e5%86%b3%e8%b5%9bwp%e8%a5%bf%e5%8d%97%e5%9c%b0%e5%8c%baweb/)

AWDP 模式：2 个 break + 1 个 fix。

| 题目 | Break 考点 | Fix 方案 |
|------|-----------|---------|
| MediaDrive | PHP 反序列化 + iconv 编码转换绕过路径黑名单 → 任意文件读 | 将 basePath 写死为 `/var/www/html/uploads/` |
| easy_time | SSRF + ZipSlip 压缩包路径穿越 → webshell | 注释掉远程头像加载和文件上传功能 |

## MediaDrive — iconv 编码转换绕过路径过滤

```php
$rawPath = $user->basePath . $f;       // basePath 来自反序列化的 cookie
$convertedPath = @iconv($user->encoding, "UTF-8//IGNORE", $rawPath);
// iconv 转换失败的字节被 IGNORE 忽略
$content = @file_get_contents($convertedPath);
```

黑名单过滤 `/flag|../|php:|data:|expect:`，但通过修改 cookie 中 `encoding` 为非标准编码，导致 `iconv` 转换时丢弃/忽略黑名单字符。

绕过：`f=fl%dfag` — 其中 `%fd` 在 iconv 转换时被忽略，拼接后变成 `flag`。

## easy_time — ZipSlip + SSRF

1. 构造 ZipSlip 恶意压缩包，文件名包含 `../../../../../../var/www/html/shell.php`
2. 上传解压后 webshell 写入目标目录
3. 通过 SSRF (远程头像加载) 访问内部 PHP 服务

flag 在 `/tmp/` 下存放。

## Connections
- Related: [[web/iconv-encoding-bypass-path-traversal]], [[web/ssrf-gopher-techniques]]
- Source: [[raw/2026长城杯AWDP半决赛WP(西南地区WEB) - Rycarls little blog]]
