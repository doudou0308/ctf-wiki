---
title: rerere — Windows PE 逆向 + 自定义 S-BOX + XOR
category: reverse
tags: [reverse-engineering, windows-pe, sbox, xor, rdata]
triggers: [rerere, 御网杯, Windows PE, S-BOX, .rdata, 逆向S-BOX, inv映射]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/reverse/rerere_Windows逆向.md]
---

## Summary

64-bit Windows PE 逆向题。从 `.rdata` 段提取 key(8B) + target(38B) + sbox(256B) → `inv[sbox[target[i]]] ^ key[i%8]` 解码。

## Key Points

### 主校验逻辑
- 入口：`check_flag` 函数
- 主校验在 `call 0x1480` 子函数中（需要跟进才能看到完整逻辑）
- 加载了三个数组：key(8B), target(38B), sbox(256B)

### 解密公式
```
flag[i] = inv_sbox[target[i]] ^ key[i % 8]
```
其中 `inv_sbox` = `{v: i for i, v in enumerate(sbox)}`

### 核心脚本
```python
key = [0xB9, 0xCD, 0xCE, 0x30, 0xB8, 0x61, 0x4E, 0xAA]
target = [0xA3, 0x5B, ...]  # 38 bytes
sbox = [0xC2, 0x23, ...]    # 256 bytes
inv = {v: i for i, v in enumerate(sbox)}
flag = ''.join(chr(inv[target[i]] ^ key[i & 7]) for i in range(38))
print(flag)
```

### 备忘
CTF 逆向常见套路：查表替换(S-BOX) + XOR。逆向 S-BOX 直接用 dict 反向映射。

## Connections
- Related: [[skills-reverse-tools]] (静态分析)
