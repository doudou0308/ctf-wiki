---
title: 办公室爱情 — 多载体隐写
category: misc
tags: [stego, word, pdf, pptx, zip, gbk, color-encoding]
triggers: [办公室爱情, 长城杯, Word隐藏文字, wbStego, PDF隐写, PPTX, 7进制颜色, GBK编码]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/misc/办公室爱情.md]
---

## Summary

Word 文档隐藏文字 + wbStego PDF 隐写 + PPTX 彩虹 7 进制颜色编码，三层隐写嵌套。

## Key Points

- Word 密码通过 document.xml 中 `w:vanish` 隐藏文字提取
- Zip 解压时 GBK 编码文件名报错，需原地替换为 ASCII 文件名（保持相同字节长度）
- PPTX 中 76 张幻灯片颜色按 7 进制编码（红=0, 橙=1, ..., 白=分隔符）

## Details

### 破解流程

1. **Word 密码提取**：`document.xml` + `w:vanish` 隐藏文字
   - `password1: True_lOve_`（可见）
   - `password12: i2_supReMe`（隐藏）
   - 合并：`True_lOve_i2_supReMe`

2. **Zip 解密**（修补 GBK 文件名）
```python
with open('皮皮特的外套.zip', 'rb') as f:
    raw = bytearray(f.read())
raw[30:41] = b'pppppp.pptx'  # 原地替换，保持 11 字节
```

3. **PPTX 颜色 7 进制解码**

## Connections

- Related: [[base64-xor-double-encoding]]
- Related: [[covert-channel-icmp-dns]]
