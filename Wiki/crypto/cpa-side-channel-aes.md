---
title: CPA 侧信道攻击 — AES-128 密钥恢复
category: crypto
tags: [side-channel, cpa, aes, hamming-weight, correlation]
triggers: [CPA, 侧信道, 功耗分析, AES, S盒, 汉明重量, 皮尔逊, pearson correlation, Z-score, trace分析]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/crypto/CPA侧信道AES密钥恢复.md]
---

## Summary

对 AES S 盒输出做汉明重量模型预测功耗，通过皮尔逊相关系数恢复密钥字节。双通道 Z-score 归一化可提高信噪比。

## Key Points

- 5000 trace × 1024 采样点 × 256 种密钥猜测的暴力 CPA
- 16 个方差峰值间距 64 采样点对应 16 次 S 盒查表
- 单通道相关度过低时，双通道 Z-score 归一化叠加
- 功耗分析核心公式：`r = Cov(P_pred, T) / (σ_pred · σ_T)`

## Details

### 核心代码

```python
import numpy as np
SBOX = [...]  # AES S 盒
HW = np.array([bin(x).count('1') for x in range(256)], dtype=np.float64)

def cpa(traces_chan):
    t_mean = traces_chan.mean(axis=0)
    t_norm = (traces_chan - t_mean) / traces_chan.std(axis=0, ddof=1)
    key = bytearray()
    for bi in range(16):
        ptb = plaintexts[:, bi]
        sbox_outs = np.array(SBOX)[np.bitwise_xor(ptb[:, None], np.arange(256))]
        hw_pred = HW[sbox_outs].T
        hw_norm = (hw_pred - hw_pred.mean(axis=1)[:, None]) / hw_pred.std(axis=1, ddof=1)[:, None]
        corr = np.abs(hw_norm @ t_norm) / 5000
        best_kg = int(corr.max(axis=1).argmax())
        key.append(best_kg)
    return bytes(key)
```

## Connections

- GHSA: [[crypto/ghsa-crypto-weakness]]
