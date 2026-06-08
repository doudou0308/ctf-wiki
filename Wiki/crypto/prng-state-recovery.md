---
title: "PRNG 状态恢复攻击"
category: crypto
tags: [prng, state-recovery, lcg, random, java, password-prediction]
triggers: [PRNG, random state recovery, LCG, 随机数状态恢复, 随机数预测, linear congruential, java random]
created: 2026-06-08
updated: 2026-06-08
sources: [raw/2026 软件系统安全赛 初赛 wp.md]
---

# PRNG 状态恢复攻击

## Summary

当服务器使用 `java.util.Random` (48-bit LCG) 生成用户密码，且注册时泄露一个随机数值时，可从泄露值逆向恢复 PRNG 内部状态，进而预测 admin 密码。

## Key Points

- `java.util.Random` 是 48-bit 线性同余生成器 (LCG)
- `nextInt()` 返回前 32 bits，可逆向推导 seed 的 48 bits
- 给定一个输出值，可穷举 17-bit 未知部分验证候选
- 恢复 seed 后即可预测任意后续随机数

## LCG 逆向公式

```python
MASK = (1 << 48) - 1
LOW47 = (1 << 47) - 1

def prev_candidates(cur):
    base = ((cur & LOW47) << 1) & MASK
    return [base, base | 1]
```

Java `Random.next(bits)` 的内部实现：
- `seed = (seed * 0x5DEECE66D + 0xB) & MASK`
- `return seed >> (48 - bits)`

逆向：已知 32-bit 输出，枚举低 16-bit → 恢复完整 48-bit seed → 向前/向后推导。

## 攻击流程

1. 注册用户 → 获取注册时返回的一个随机数 `x`
2. 枚举 `x` 对应的 2^16 个候选 seed
3. 用候选 seed 生成 admin 密码 → 逐一尝试验证
4. 拿到 admin 密码 → 登录进入 Thymeleaf SSTI 利用

## Connections

- Related: [[crypto/skill-prng]], [[web/thymeleaf-ssti-fragment-expression]]
- Source: [[ctf/2026-software-security-competition-qualifier]]
