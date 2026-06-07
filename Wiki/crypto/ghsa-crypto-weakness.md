---
title: GHSA — 加密/随机数漏洞（Crypto）
category: crypto
tags: [ghsa, crypto, entropy, prng, nonce, rsa, signature, authentication]
triggers: [熵不足, 弱随机数, PRNG, nonce重用, 加密缺陷, JWT密钥薄弱, 随机数种子, insufficient entropy, weak PRNG, 加密审计]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/crypto/GHSA-*]
---

## Summary

加密、随机数生成、签名验证等密码学相关的安全漏洞合集。

**总量**: crypto/ 下 15 篇 GHSA-* 文件 + web/code-audit 下约 20 篇涉及加密/随机数

## Common Patterns

### 弱随机数模式

| 模式 | 描述 | 危险函数 |
|------|------|---------|
| **math/rand (非 crypto/rand)** | 使用非密码学安全的 PRNG | Go: `math/rand`, Java: `Random()` |
| **time-based 种子** | 用时间戳作为随机数种子 | `srand(time(NULL))` |
| **nonce 重用** | 签名/验证中 nonce 被复用 | ECDSA k 重用 |
| **状态参数过短** | OAuth state / PKCE code_verifier 长度不足 | < 128 bit |

### 加密缺陷模式

| 模式 | 描述 | 典型场景 |
|------|------|---------|
| **弱密钥** | JWT secret 为空/薄弱 | `shared-secret` 未配置 |
| **算法降级** | 接受弱算法（MD5/SHA1/RC4） | 签名验证回退 |
| **不安全的密钥交换** | OIDC nonce 熵不足 | replay 攻击 |
| **硬编码密钥** | 密钥直接写在代码/配置中 | 反编译可提取 |

## Representative GHSA Entries

| GHSA | CVE | Severity | Package | Brief |
|------|-----|----------|---------|-------|
| GHSA-2m7h-86qq-fp4v | CVE-2022-31034 | high (8.4) | Argo CD | PKCE/OAuth 参数弱随机 |
| GHSA-6f9g-cxwr-q5jr | — | critical (9.8) | Jenkins CLI | 任意文件读→凭证窃取 |
| GHSA-7ppg-37fh-vcr6 | — | critical (9.8) | Milvus | 未授权 API（无加密） |
| GHSA-2g22-wg49-fgv5 | — | critical (10.0) | XWiki | SQLi → 数据泄露 |

## Connections

- Related: [[ecdsa-nonce-reuse]]
- Related: [[rsa-low-exponent-attack]]
- Related: [[cpa-side-channel-aes]]
- Related: [[influxdb-unauthorized-access]] (JWT 空密钥)
- Related: [[xor-known-plaintext-docx]]
