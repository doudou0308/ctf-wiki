---
title: 迷宫 — 嵌套压缩包 + Base64 干扰字符
category: misc
tags: [nested-zip, base64, archive-extraction]
triggers: [嵌套压缩包, 迷宫, 多层解压, Base64干扰, 6d前缀]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/misc/迷宫_嵌套压缩包.md]
---

## Summary

多层嵌套压缩包逐层解压 → 最内层 BIN 文件中含非标准 Base64 → 去除前缀 `6d` 干扰字符 → 标准 Base64 解码。

## Key Points

- 嵌套压缩包类题目考验耐心
- 非标准 Base64 需观察特征去除干扰字符后再解码

## Details

### 核心代码

```python
import base64
raw = "6d..."  # BIN 文件内容
clean = raw.replace("6d", "")  # 去除干扰
flag = base64.b64decode(clean).decode()
```

## Connections

- Related: [[damaged-zip-base64]]
- Related: [[base64-xor-double-encoding]]
