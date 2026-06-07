---
title: 魔改凯撒 + XOR — 两段拼接 leet-speak
category: reverse
tags: [caesar, xor, leet-speak, two-part-flag, rc4]
triggers: [魔鬼凯撒, 凯撒, leet, leet-speak, shift 20, XOR 0xDE, 两段拼接, RC4, 长城杯]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/reverse/魔鬼凯撒的RC4茶室.md]
---

## Summary

flag 分两段：① flag 文件 XOR 0xDE 得 leet-speak 前半段 ② 二进制内硬编码字符串经魔改 Caesar（字母 shift 20 / 数字 shift 8）解得后半段。

## Key Points

- flag 本身就是 leet-speak 形式，无需额外映射
- 魔改 Caesar 参数：字母 shift +20（加密）/ -20（解密），数字 shift +8 / -8

## Details

### 核心代码

```python
# Part 1: XOR 0xDE on flag file
part1_bytes = bytes([c ^ 0xDE for c in open('flag', 'rb').read()])
part1 = part1_bytes[6:].decode()  # "x1aom1ng_1s_3o_easy_"

# Part 2: Caesar decode
enc = 'z8layn_b91_nb9ha1}'
part2 = ''
for c in enc:
    if 'a' <= c <= 'z':
        part2 += chr((ord(c) - 97 - 20 + 26) % 26 + 97)
    elif '0' <= c <= '9':
        part2 += chr((ord(c) - 48 - 8 + 10) % 10 + 48)
    else:
        part2 += c
# → "f0rget_h13_th1ng3}"
```

## Connections

- Related: [[simple-smc]]
- Related: [[windows-pe-sbox-xor]]
