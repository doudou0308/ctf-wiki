---
title: ECDSA nonce 重用攻击
category: crypto
tags: [ecdsa, nonce, private-key-recovery, elliptic-curve]
triggers: [ECDSA, nonce重用, signature reuse, SECP256k1, 签名r相同, 私钥恢复, k重用]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/crypto/ECDSA_nonce重用.md]
---

## Summary

两次 ECDSA 签名使用相同 nonce k 导致签名 r 相同，可恢复私钥 d。

## Key Points

- 数学本质：两式相减消去 r·d 项求 k，再代入任一式求 d
- 不要忘记消息需要先 SHA-256 哈希再参与计算

## Details

### 核心公式

- `s = k⁻¹·(h + r·d) mod n`
- `k = (h₁−h₂) / (s₁−s₂) mod n`
- `d = (s₁·k − h₁) / r mod n`

### 核心代码

```python
from ecdsa import SECP256k1
n = SECP256k1.order
h1 = int.from_bytes(hashlib.sha256(m1).digest(), 'big')
h2 = int.from_bytes(hashlib.sha256(m2).digest(), 'big')
k = ((h1 - h2) * pow(s1 - s2, -1, n)) % n
d = ((s1 * k - h1) * pow(r, -1, n)) % n  # 私钥
```

## Connections

- Related: [[rsa-low-exponent-attack]]
- GHSA: [[crypto/ghsa-crypto-weakness]]
