---
title: ChaCha20 APK Native 逆向
category: reverse
tags: [android, jni, chacha20, native, ida]
triggers: [ChaCha20, APK, JNI, .data.rel.ro, JNINativeMethod, NativeBridge, seek(64), counter]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/reverse/ChaCha20_APK_Native逆向.md]
---

## Summary

APK JNI Native 逆向 → 从 `.data.rel.ro` 读取 JNINativeMethod 表 → PIC 基址计算 → 提取 ChaCha20 key/nonce → 注意 counter 起点。

## Key Points

- 用 JADX 看 MainActivity 找到 `NativeBridge.c()`
- 用 IDA 从 `.data.rel.ro` 的 gMethods 表反查函数地址
- PyCryptodome 默认 counter=0，但程序实际 counter=1，需 `.seek(64)` 跳过第一个 keystream block

## Details

### 核心代码

```python
from Crypto.Cipher import ChaCha20

key = bytes.fromhex('149263a16f2d89cbf0375b1ca94e78d3226017ee9abc4d0853e1762a8dc4903f')
nonce = bytes.fromhex('44332211abcdef668899aa55')
ct = bytes.fromhex('d097c3f6d279df23af24ad35e9e08793831c8e2a22a1b2968b')
cipher = ChaCha20.new(key=key, nonce=nonce)
cipher.seek(64)  # counter starts at 1
flag = cipher.decrypt(ct)
```

ChaCha20 是对称加密，encrypt = decrypt。别忘了 seek(64) 跳过 counter=0 块。

## Connections

- Related: [[des-apk-rodata]]
- Related: [[pyinstaller-cython-reverse]]
