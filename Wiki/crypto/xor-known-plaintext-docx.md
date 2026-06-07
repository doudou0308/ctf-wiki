---
title: XOR 已知明文攻击 — DOCX 文件恢复
category: crypto
tags: [xor, known-plaintext, zip, docx, rot13]
triggers: [XOR, 已知明文攻击, many-time pad, DOCX, ZIP头, PK0304, ROT13, XOR密钥恢复]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/crypto/XOR已知明文攻击_DOCX恢复.md]
---

## Summary

DOCX = ZIP 压缩包 → 固定头 `PK\x03\x04` → 已知明文攻击恢复 XOR 密钥 → 解密后还需 ROT13 二次解码。

## Key Points

- XOR 退化为 many-time pad 即完全可破
- 任何固定结构的文件格式（ZIP/PNG/BMP）都适合做已知明文攻击
- 注意可能有多层编码需要逐层处理

## Details

### 核心代码

```python
data = open('task.docx.enc', 'rb').read()
key = bytes([c ^ p for c, p in zip(data[:4], bytes.fromhex('504B0304'))])
decrypted = bytes([data[i] ^ key[i % 4] for i in range(len(data))])
open('task.docx', 'wb').write(decrypted)

# 如有 ROT13 二次编码
import codecs
flag = codecs.decode(flag_enc, 'rot13')
```

## Connections

- Related: [[base64-xor-double-encoding]]
- Related: [[misc/damaged-zip-base64]]
