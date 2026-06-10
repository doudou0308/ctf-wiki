---
title: "2026 CISCN & 长城杯半决赛 AWDP-PWN WP"
category: ctf
tags: [ctf, ciscn, 长城杯, awdp, pwn, writeup, house-of-storm, house-of-cat]
triggers: [ciscn awdp, 长城杯半决赛pwn, awdp pwn]
created: 2026-06-10
updated: 2026-06-10
sources: [raw/2026 CISCN&长城杯半决赛 AWDP-PWN-先知社区.md]
---

# 2026 CISCN & 长城杯半决赛 AWDP-PWN WP

> 来源：先知社区 | 4 题 (所有 break + fix)

## catchme — UAF + House of Storm (glibc 2.27)

- UAF: free 后 show 泄露 libc; edit 从 8 字节偏移处开始写
- 堆地址以 `0x56` 开头即可用 House of Storm
- Fix: free 后清空指针

## broken_message — UAF + Heap Key 泄露 + 备用栈

- 自定义堆管理器（单链表 + 指针保护 XOR key）
- 注册 sigsegv handler + 备用栈（在 heap 上）
- 方法：double free → show → heap^key; 写入 `0x80808080` → 触发段错误 → 打印错误地址 → 算出 key → 算出 heap → 任意地址读写 → 泄露 libc → ret2system
- Fix: 清空 heap 而非清空 size

## easy_rw_revenge — RC4 前端 + Largebin Attack → House of Cat

- 前端 RC4 加密 + 后端 MD5 爆破 (3字节) → cookie
- 漏洞: `size` 为 `int` 类型 → 负数绕过大小限制 → malloc 失败但地址未清 → UAF
- 仅 0x500+ 堆块可 UAF → largebin attack → `_IO_list_all` → House of Cat ORW
- Fix: 有符号跳转 → 无符号跳转

## minidb — 引用计数绕过 UAF → House of Cat

- 正常逻辑引用为 0 才 free; 但某分支绕过引用检查直接 free → UAF
- 无 edit 功能 → 通过预留指针堆叠修改已 free 堆块
- Fix: 修复引用计数检查

## Connections
- Related: [[pwn/skill-advanced-exploits]], [[pwn/io-file-stdout-fsop]]
- Source: [[raw/2026 CISCN&长城杯半决赛 AWDP-PWN-先知社区]]
