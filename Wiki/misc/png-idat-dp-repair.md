---
title: "PNG IDAT 损坏 DP 修复"
category: misc
tags: [png, stego, idat, dp, image-repair, crc32, lsb, zero-width, 隐写, 图像修复]
triggers: [PNG IDAT repair, PNG损坏, IDAT错位, CRC32爆破, 零宽字符, LSB隐写, dynamic programming png]
created: 2026-06-08
updated: 2026-06-08
sources: [raw/2026 软件系统安全赛 初赛 wp.md]
---

# PNG IDAT 损坏 DP 修复

## Summary

当 PNG 文件的 IDAT 数据块错位导致图像损坏时，可使用动态规划 (DP) 枚举每行 filter_byte 的偏移量，选取最优路径重建图像。修复后通过 LSB 隐写提取 ZIP → CRC32 爆破密码 → 解压得到零宽字符编码的 flag。

## 攻击链

```
1. 去除 PNG 文件头部干扰数据 → libmagic 识别
2. IDAT CRC 校验失败 → 数据错位
3. DP 枚举每行 filter_byte 偏移 → 重建图像
4. LSB RGB 低位提取 → ZIP 文件
5. CRC32 爆破 5 字节密码 → 解压 flag.txt
6. 零宽字符 (U+200B/U+200C) → 01 编码 → flag
```

## DP 核心算法

```python
# dp[r][d] = 第 r 行偏移量为 d 时的最优得分
# 得分 = 该行首字节是否为合法 filter_type (0-4)
# 转移: d 与前一行偏移量 pd 差距 ≤ 12
# 终点: 选择 penalty = abs(d - max_delta) * 0.2 最小的路径
for r in range(1, height):
    for d in range(max_delta + 1):
        best = max(prev[pd] for pd in range(max(0, d-12), d+1))
        cur[d] = best + (1 if raw[idx] <= 4 else 0)
```

重建后通过 `reconstruct_scanline(filter_type, filtered, prior, channels)` 逐行还原像素。

## CRC32 爆破 ZIP 密码

ZIP 中小文件 (≤5 bytes) 可通过 CRC32 碰撞暴力破解密码。本题密码为 5 段短字符串：`pass\n is\nc1!x\nxtLf\n%fXY\nPkaA\n`。

## 零宽字符解码

```python
zw = ''.join(ch for ch in text if ch in '\u200b\u200c')
bits = ''.join('0' if ch == '\u200b' else '1' for ch in zw)
flag = bytes(int(bits[i:i+8], 2) for i in range(0, len(bits)//8*8, 8))
```

## Connections

- Related: [[forensics/multi-layer-steganography]], [[misc/behinder-traffic-decryption]]
- Source: [[ctf/2026-software-security-competition-qualifier]]
