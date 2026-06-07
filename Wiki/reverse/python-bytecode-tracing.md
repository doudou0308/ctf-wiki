---
title: Python 字节码追踪 — pyc 逆向
category: reverse
tags: [python, pyc, marshal, dis, bytecode]
triggers: [pyc, marshal.loads, dis.dis, 字节码, Python 3.12, wordcode, magic number]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/reverse/Python字节码追踪.md]
---

## Summary

Python 3.12 pyc 文件逆向 — `marshal.loads` + `dis.dis` 还原字节码 → `base64.b64decode` + XOR 解密。

## Key Points

- Python 版本敏感的 pyc 文件必须用完全相同版本的 Python 反汇编
- magic number 前 2 字节标识版本
- Python 3.11 的 dis 无法解析 3.12 的 wordcode 格式

## Details

### 核心代码

```python
import base64
encoded = 'OjA9Oydua2skJjA0JnE+NmVkcTIpb2lxNCZrZHEqLj1rPSw/azJsOjYh'
key = 92
decoded = base64.b64decode(encoded)
flag = ''.join(chr(b ^ key) for b in decoded)
```

## Connections

- Related: [[pyinstaller-cython-reverse]]
- Related: [[base64-xor-double-encoding]]
