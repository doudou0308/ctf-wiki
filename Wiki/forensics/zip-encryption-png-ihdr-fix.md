---
title: ZIP 加密破解 + PNG IHDR 高度隐藏修复
category: forensics
tags: [zip-encryption, png, ihdr, aes-zip, pyzipper, crc-fix]
triggers: [ZIP加密, WinZip AES, compress_type=99, pyzipper, PNG修复, IHDR高度, 弱密码 202501, 日期前缀密码, 御网杯]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/forensics/ZIP加密_PNG修复_IHDR高度隐藏.md]
---

## Summary

WinZip AES 加密 ZIP（compress_type=99）→ 弱密码 `202501` → pyzipper 解密 → PNG 签名修复（前 4 字节）→ IHDR 高度 400→800 修复 → 找回被隐藏的下半部分图像。

## Key Points

- Python `zipfile` 不支持 `compress_type=99`（AES 加密），必须用 `pyzipper`
- 文件名为 `202501_secret.png`，尝试日期前缀 `202501` 作为密码
- PNG 高度隐藏技巧：IDAT 数据完整但 IHDR 高度被改小

## 核心脚本

```python
import pyzipper, struct, zlib

with pyzipper.AESZipFile('backup_2025Q1.zip', 'r') as zf:
    zf.setpassword(b'202501')
    data = bytearray(zf.read('202501_secret.png'))

# PNG 签名修复
data[0:4] = bytes.fromhex('89504e47')

# IHDR 高度修复 400→800
struct.pack_into('>I', data, 20, 0x320)

# IHDR CRC 重算
crc = zlib.crc32(data[12:29]) & 0xFFFFFFFF
struct.pack_into('>I', data, 29, crc)
```

## 备忘

- 实际高度估算：`IDAT 数据量 / (宽度 × 3 + 1)`
- IHDR 字段偏移：宽度 +4（offset 16），高度 +8（offset 20）
- pyzipper 支持 AES 加密 ZIP

## Connections

- **Related:** [[damaged-zip-base64]] — 损坏压缩包 + Base64 编码
- **Related:** [[maze-nested-zip]] — 嵌套压缩包
- **Source:** [ctf-kb/forensics/ZIP加密_PNG修复_IHDR高度隐藏.md](file:///c:/Users/ZZH/.trae/ctf-kb/forensics/ZIP加密_PNG修复_IHDR高度隐藏.md)
