---
title: "iconv 编码转换绕过路径遍历过滤"
category: web
tags: [path-traversal, iconv, encoding, bypass, php, waf-bypass]
triggers: [iconv, 编码转换, encoding bypass, path traversal bypass, iconv IGNORE, UTF-8//IGNORE, fl%dfag]
created: 2026-06-09
updated: 2026-06-09
sources: [raw/2026长城杯AWDP半决赛WP(西南地区WEB) - Rycarls little blog.md]
---

# iconv 编码转换绕过路径遍历过滤

## Summary

当 PHP 应用使用 `iconv()` 将用户输入转换为 UTF-8 时，如果设置 `//IGNORE` 标志，非法字节序列会被静默丢弃。攻击者可利用这一特性绕过路径黑名单过滤。

## 原理

```php
$convertedPath = @iconv($user->encoding, "UTF-8//IGNORE", $rawPath);
```

- `iconv()` 从源编码 `$user->encoding` 转换到 `UTF-8`
- `//IGNORE` 标志：遇到无效字节序列时直接忽略，不报错
- 攻击者控制 `$user->encoding`（如从 cookie 反序列化获取），选择非标准编码
- 在黑名单关键词（如 `flag`）中间插入非法字节，iconv 转换时丢弃该字节，还原为关键词

## 绕过示例

```
黑名单: /flag|../|php:|data:|expect:/

原始路径: /var/www/uploads/fl%fdag
iconv 转换后: /var/www/uploads/flag
```

关键字符 `%fd` 在 iconv 转换时被 `//IGNORE` 丢弃，拼接后形成 `flag`。

## 检测与防御

**检测：**
- 检查应用中是否存在 `iconv()` + `//IGNORE` 组合
- 检查 `encoding` 参数是否来自用户可控的输入（如 cookie、session）
- 路径过滤应作用于转换后的路径，而非原始路径

**防御：**
- 固定编码为 `UTF-8`，不从外部获取 `encoding` 参数
- 避免使用 `//IGNORE`，改用 `//TRANSLIT` 或移除该标志
- 路径白名单而非黑名单

## Connections
- Related: [[web/file-protocol-redis-crlf-pickle-rce]], [[web/ssrf-gopher-techniques]]
- Source: [[ctf/changchengbei-awdp-2026-semi]]
