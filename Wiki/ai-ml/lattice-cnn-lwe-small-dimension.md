---
title: LatticeCNN — 小维度 LWE 噪声枚举 + CNN 特征提取 + AES 密钥推导
category: ai-ml
tags: [lwe, lattice, cnn, noise-enumeration, aes-key-derivation, linear-equation, small-dimension]
triggers: [LWE, Lattice, 格密码, LatticeCNN, CNN特征, 小噪声, 穷举误差, 5^8枚举, secret向量, 模q解方程, SHA256 AES, AES-128-ECB, .npy解析, 卷积+池化, MIX矩阵, 中心化模数]
created: 2026-06-07
updated: 2026-06-07
sources: [raw/第三届"长城杯"网数智安全大赛(防护赛)决赛-部分WP.md]
---

## Summary

CNN-like 模型本质是公开卷积核和混合矩阵的特征提取 + 隐藏向量线性输出 + 小噪声。因维度仅 8 维、噪声仅 [-2,2]，可在 `5^8 = 390625` 种中枚举噪声解线性方程组恢复隐藏向量，再推导 AES 密钥解密 flag。

## Key Points

### 数学本质

```
b_i = <a_i, secret_s> + e_i (mod q)
```

- `q = 65537`（素数）
- `secret_s` 是 8 维隐藏向量
- `e_i` ∈ [-2, 2]（小噪声）
- 已知 24 组 `(input_i, output_i)`

这本质是 **小维度 LWE 问题**，维度过小（8）可暴力枚举。

### 求解策略

选择 8 条方程 → 枚举 8 个噪声取值（`5^8 = 390625` 种）→ 解线性方程组 → 用全部 24 条方程验证误差是否都落在 [-2, 2]。

```python
import itertools

for indexes in itertools.combinations(range(24), 8):
    inv = inverse_matrix_mod([a_rows[i] for i in indexes], q)
    if inv is None:
        continue
    selected_b = [b_rows[i] for i in indexes]
    for errors in itertools.product(range(-2, 3), repeat=8):
        rhs = [(b - e) % q for b, e in zip(selected_b, errors)]
        secret = matrix_vector_mod(inv, rhs, q)
        # 验证全部 24 条
        all_errors = [centered_mod(b - dot(a, secret), q) for a, b in zip(a_rows, b_rows)]
        if all(-2 <= e <= 2 for e in all_errors):
            return secret  # 找到
```

### 恢复结果

| 类型 | 值 |
|------|-----|
| secret_mod | `[17, 65525, 9, 65522, 6, 14, 65529, 11]` |
| secret_signed | `[17, -12, 9, -15, 6, 14, -8, 11]` |

### AES 密钥推导

```python
secret_str = "17,-12,9,-15,6,14,-8,11"  # 逗号拼接
key = hashlib.sha256(secret_str.encode()).digest()[:16]
# AES-128-ECB 解密 cipher.bin + 去除 PKCS7 padding
```

### 特征提取（CNN-like）

CNN 部分全部公开，可以直接复现：
1. `conv_valid(x, K1)` → ReLU → `avgpool2x2` → 展平
2. `conv_valid(x, K2)` → ReLU → `avgpool2x2` → 展平
3. `MIX @ base % q` → 8 维特征向量 `a_i`

所有参数（K1, K2, MIX）在 `public.json` 中公开。

## Connections
- Related: [[rsa-low-exponent-attack]] (模运算 + 小参数枚举)
- Related: [[hastad-coppersmith-linear-padding]] (Coppersmith/格攻击)
- Source: [[raw/第三届"长城杯"网数智安全大赛(防护赛)决赛-部分WP]]
