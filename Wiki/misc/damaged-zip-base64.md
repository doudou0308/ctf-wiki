---
title: 损坏压缩包 — Base64 解码
category: misc
tags: [zip, base64, damaged-archive]
triggers: [压缩包损坏, Base64, ZIP, 签到题, 手动提取]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/misc/损坏压缩包_Base64编码.md]
---

## Summary

损坏的 ZIP 包中提取 `.txt` → Base64 解码 → flag。

## Key Points

- "损坏的压缩包"类签到题通常只需要手动提取内容
- Base64 特征：字母数字 + `/` + `=` 结尾
- 直接解压可能失败，需要用文本查看工具查看 ZIP 内部文件

## Details

```bash
echo "xxx_base64_string" | base64 -d
```

## Connections

- Related: [[maze-nested-zip]]
- Related: [[base64-xor-double-encoding]]
