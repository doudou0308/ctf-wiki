---
title: "2026 软件安全赛半决赛 PWN Robo_admin WP"
category: ctf
tags: [ctf, 软件安全赛, pwn, writeup, awdp, house-of-some]
triggers: [Robo_admin, 软件安全赛半决赛, 软件安全赛pwn]
created: 2026-06-10
updated: 2026-06-10
sources: [raw/2026软件安全赛半决赛PWN Robo_admin WP fix&break-先知社区.md]
---

# 2026 软件安全赛半决赛 PWN — Robo_admin

> 来源：先知社区 | AWDP 模式

## Break — 格式化字符串泄露密码 + off-by-one unlink → House of Some

### 阶段一：ASCII 转义绕过 → 格式化字符串

`set_notice` 函数有 `str` 转义函数，输入 `\x25\x24p` 等 ASCII 编码值可绕过对 `%` 和 `$` 的过滤，触发格式化字符串漏洞，泄露 passwd。

```python
payload = b'\\x256\\x24p'  # → %6$p
payload += b'\\x257\\x24p'  # → %7$p
```

### 阶段二：off-by-one → unlink → 泄露 libc + heap

进入 admin 功能后存在 off-by-one 漏洞，构造堆布局实现 unlink，泄露 libc 和 heap 地址。

### 阶段三：House of Some — 覆盖 IO_list_all

把堆块开到 `IO_list_all` 上，通过 House of Some 手法写 FILE 结构体完成利用。

```python
from SomeofHouse import HouseOfSome
```

## Fix

- 修复格式化字符串：过滤 `%` 和 `$` 字符
- 修复 off-by-one：严格检查输入长度

## Connections
- Related: [[pwn/skill-advanced-exploits]], [[pwn/io-file-stdout-fsop]]
- Source: [[raw/2026软件安全赛半决赛PWN Robo_admin WP fix&break-先知社区]]
