---
title: 多层隐写综合（Arnold + LSB + 零宽 + PYC 逆向）
category: forensics
tags: [steganography, arnold-cat-map, lsb, zero-width-characters, pyc-reverse, rot-xor]
triggers: [Arnold猫映射, 图像置乱, LSB嵌入, 零宽字符, ZW解码, PYC逆向, ROT XOR, 多层隐写, 御网杯]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/forensics/多层隐写_Arnold_LSB_零宽_PYC.md]
---

## Summary

三层隐藏 + 一重加密：① poster.png Arnold 置乱 7 次 + LSB 嵌入 QR 码（前半密钥）② notice.docx 零宽字符编码 8 字节（后半密钥）③ exfil_tool.pyc 中存储 ROT+XOR 加密的 flag → 组合 16 字节密钥解密。

## 隐藏层级

| 层级 | 载体 | 技术 | 承载数据 |
|------|------|------|----------|
| ① | poster.png | Arnold 置乱 7 次 + LSB | QR 码（前半 8 字节密钥） |
| ② | notice.docx | 零宽字符编码 | 后半 8 字节密钥 |
| ③ | exfil_tool.pyc | ROT + XOR | 加密的 flag |

## 核心脚本

```python
# Arnold 逆变换
def arnold_inverse(img, n):
    W = img.width
    result = img.copy()
    for _ in range(n):
        src = result.copy().load(); dst = result.load()
        for x in range(W):
            for y in range(W):
                nx, ny = (x+y)%W, (x+2*y)%W
                dst[x,y] = src[nx,ny]
    return result

# ROT + XOR 解密
def rot_xor_decrypt(data, key):
    out = bytearray()
    ROT = (0,1,2,3,4,5,6,7)
    for i in range(len(data)):
        s = ROT[i % 8]
        r = data[i] ^ key[i % len(key)]
        r = ((r >> s) | (r << (8-s))) & 255
        out.append(r)
    return bytes(out)

# ZW 解码：U+200B=00, U+200C=01, U+200D=10, U+FEFF=11
# LSB 提取：每像素 3 bits(R/G/B LSB)，前 4 字节 BE 长度
# 组合：combo_key = QR_KEY + ZW_KEY → rot_xor_decrypt(CIPHER, combo_key)
```

## 备忘

- Arnold 猫映射迭代周期与图像尺寸相关，512×512 图像周期 = 384
- 零宽字符在 XML 中有对应 Unicode 实体
- ROT+XOR 加密先循环左旋再 XOR
- 工具链：pyc 逆向 → Arnold 逆变换 → LSB 提取 → ZW 解码 → ROT/XOR 解密

## Connections

- **Related:** [[covert-channel-icmp-dns]] — ICMP/DNS 隐写信道
- **Related:** [[data-leak-trace-purge]] — 数据泄露溯源
- **Related:** [[base64-xor-double-encoding]] — Base64 + XOR 双层编码
- **Related:** [[office-love-multi-stego]] — 办公室爱情多层隐写
- **Source:** [ctf-kb/forensics/多层隐写_Arnold_LSB_零宽_PYC.md](file:///c:/Users/ZZH/.trae/ctf-kb/forensics/多层隐写_Arnold_LSB_零宽_PYC.md)
