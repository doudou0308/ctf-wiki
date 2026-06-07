---
title: BASE64 + XOR 双层编码
category: misc
tags: [base64, xor, encoding, known-plaintext]
triggers: [BASE64, XOR, 双层编码, 已知明文, flag首字符, 密钥恢复]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/misc/BASE64_XOR双层编码.md]
---

## Summary

先 Base64 解码，再用明文首字符 `'f'`（0x66）异或恢复密钥，全量解密。

## Key Points

- CTF 中 BASE64+XOR 组合极常见
- 突破口：已知明文首字符/格式头恢复密钥字节
- 整个字符串都需要 Base64 解码，不是分段

## Details

### 核心代码

```python
import base64
decoded = base64.b64decode(ct_base64)
key = decoded[0] ^ ord('f')  # flag 首字符必为 'f'
flag = ''.join(chr(b ^ key) for b in decoded)
```

## Connections

- Related: [[xor-known-plaintext-docx]]
- Related: [[maze-nested-zip]]
