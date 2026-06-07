---
title: RSA 低指数攻击（e=3）
category: crypto
tags: [rsa, low-exponent, coppersmith, gmpy2]
triggers: [RSA, 低指数, e=3, iroot, 开立方, Coppersmith, Hastad, gmpy2]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/crypto/BabyRSA_低指数攻击.md]
---

## Summary

`e=3` 且 `m³ < n` 时，密文 `c` 直接等于 `m³`，对 `c` 开立方即可得明文。

## Key Points

- 先计算 `c` 的 bit 长度，若约等于 `n` 的 1/3 则直接开立方
- 若 `m³ > n` 则需要用 Coppersmith 或 Hastad Broadcast

## Details

### 核心代码

```python
import gmpy2
from Crypto.Util.number import long_to_bytes

m = gmpy2.iroot(c, e)[0]  # 精确开立方根
flag = long_to_bytes(m).decode()
```

`gmpy2.iroot(c, e)` 返回 `(root, is_exact)`，`[0]` 取值，`[1]` 验证是否为精确根。

## Connections

- Related: [[ecdsa-nonce-reuse]]
- GHSA: [[crypto/ghsa-crypto-weakness]]
