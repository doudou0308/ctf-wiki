---
title: SMC 自修改代码 + GF(2) 线性变换
category: reverse
tags: [smc, xor, gf2, linear-algebra, gaussian-elimination]
triggers: [SMC, 自修改代码, GF(2), 线性变换, 矩阵求逆, 高斯消元, init_array, F1@gChe]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/reverse/SimpleSMC.md]
---

## Summary

SMC 双层 XOR 解密 + GF(2) 线性变换矩阵求逆。使用函数序言 `55 48 89 e5 48 83 ec` 反推 XOR 密钥。

## Key Points

- 两层 XOR 顺序不可变
- GF(2) 线性变换奇数次不等同于还原，需矩阵求逆

## Details

### 解题要点

1. 用函数序言 `55 48 89 e5 48 83 ec` 反推 7 字节密钥 `F1@gChe`
2. 构建 32×32 GF(2) 矩阵，高斯消元求解 `T^65 * flag = check`
3. 两层 XOR：init_array 先用固定数据做第一层，flag[21:28] 异或做第二层

```python
# GF(2) 高斯消元求解
# Flag: flag{d0_y0u_Kn*w_5mC_F1@gCheCk?}
```

## Connections

- Related: [[caesar-rc4-leet-speak]]
- Related: [[windows-pe-sbox-xor]]
