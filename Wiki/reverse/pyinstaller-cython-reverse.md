---
title: PyInstaller 解包 + Cython .pyd 逆向
category: reverse
tags: [pyinstaller, cython, pyd, reverse-engineering, base64-custom]
triggers: [PyInstaller, pyinstxtractor, .pyd, Cython, uncompyle6, custom base64, 长城杯, baby_re]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/reverse/baby_re.md]
---

## Summary

PyInstaller 打包 + Cython 编译的 .pyd 逆向题。解包后 .pyd 因 Python 版本不匹配无法 import，需用 strings 提取关键数据（变表 base64、密钥、target）后手动解密。

## Key Points

- `pyinstxtractor` 解包 → `uncompyle6` 反编译 run.pyc → 理解逻辑
- `.pyd` 文件跨 Python 版本无法 `import`（3.7 编译的 pyd 在 3.11 无法加载）
- 直接用 `strings` 从 .pyd 提取：变表、标准表、密钥、target
- 解密链：`target ⟶ custom_b64→std_b64 映射 ⟶ base64 解码 ⟶ XOR 密钥 ⟶ flag`

## Details

### 核心脚本

```python
import base64

target = '5WEU5ROREb0hK+AurHXCD80or/h96jqpjEhcoh2CuDh='
std_b64 = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/'
custom_b64 = 'uKbnhWvcAesT74M6D2CU/EjrgLYo50GiOtFPXI1HaB3yZqkd+JSR8lzVNpwf9xQm'
key_raw = 'H7jKYRfBu7cckJwqviiqYXZI8h7Yp0d2'

decoded = base64.b64decode(target.translate(str.maketrans(custom_b64, std_b64)))
flag_content = bytes(a ^ b for a, b in zip(decoded, key_raw.encode())).decode()
print(f'flag{{{flag_content}}}')
```

## Connections

- Related: [[python-bytecode-tracing]] (pyc 逆向)
- Related: [[base64-xor-double-encoding]]
