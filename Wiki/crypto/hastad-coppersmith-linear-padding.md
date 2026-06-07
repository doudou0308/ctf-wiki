---
title: Hastad Broadcast + Coppersmith 线性填充 RSA
category: crypto
tags: [rsa, hastad, coppersmith, crt, linear-padding, sage]
triggers: [Hastad, Coppersmith, CRT, 线性填充, 仿射变换, (a*m+b)^e, small_roots, 同一明文多模数]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/crypto/Hastad_Coppersmith_线性填充.md]
---

## Summary

同一明文 m 用 e=3 个不同模数加密，每次带仿射变换 (a_i·m + b_i)^e mod n_i。展开多项式 → CRT 合并 → Coppersmith 小根求解。

## Key Points

- 不是标准 Hastad（c = m^e mod n_i），多了已知的 a_i·m + b_i 线性填充
- 展开 (a·m + b)^3 = a^3·m^3 + 3a^2b·m^2 + 3ab^2·m + b^3
- 每个 n_i 一个同余式，CRT 合并系数 → 单一三次方程 → Coppersmith 求解

## 解题脚本

```python
from sage.all import *

# 展开 (a*m+b)^3 = a^3·m^3 + 3a^2b·m^2 + 3ab^2·m + b^3
# 每个 n_i 一个同余式: A_i·m^3 + B_i·m^2 + C_i·m + D_i ≡ 0 (mod n_i)
# CRT 合并系数 → mod N = n1·n2·n3 的单一三次方程
N = n1 * n2 * n3
A = CRT([A1, A2, A3], [n1, n2, n3])
# ... B, C, D 同理
Ainv = inverse_mod(A, N)
P.<x> = PolynomialRing(Zmod(N))
f = x^3 + (B*Ainv)%N * x^2 + (C*Ainv)%N * x + (D*Ainv)%N
roots = f.small_roots(X=1<<512, beta=0.3)
m = int(roots[0])
```

## 备忘

- Sage 的 `small_roots` 默认使用 Coppersmith 定理
- 关键参数：`X` 是根的上界，`beta` 控制因式大小
- 需要 SageMath 环境运行

## Connections

- **Related:** [[rsa-low-exponent-attack]] — RSA 低指数攻击基础
- **Related:** [[ghsa-crypto-weakness]] — 加密/随机数漏洞合成
- **Source:** [ctf-kb/crypto/Hastad_Coppersmith_线性填充.md](file:///c:/Users/ZZH/.trae/ctf-kb/crypto/Hastad_Coppersmith_线性填充.md)
