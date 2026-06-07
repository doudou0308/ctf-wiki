---
title: Unity IL2CPP — Themida 壳内存 Dump + AES-CBC 解密
category: reverse
tags: [unity, il2cpp, themida, memory-dump, aes-cbc, global-metadata, GameAssembly]
triggers: [Unity IL2CPP, GameAssembly.dll, Themida壳, global-metadata.dat, 内存dump, check_flag, AESEncrypt, GetRealKey, dummy flag, UnityMain2, IL2CPP逆向, dumbcpp]
created: 2026-06-07
updated: 2026-06-07
sources: [raw/第三届"长城杯"网数智安全大赛(防护赛)决赛-部分WP.md]
---

## Summary

Unity IL2CPP 编译的游戏 `dumbcpp.exe`（仅 Unity 启动器）。核心逻辑在 `GameAssembly.dll`（被 Themida 保护）和 `global-metadata.dat`（IL2CPP 元数据）中。运行时 dump 内存中的 `GameAssembly.dll` 绕过 Themida 壳，定位到 AES-CBC 验证逻辑。还有假 flag 干扰。

## Key Points

### 架构定位

```
dumbcpp.exe      → Unity 启动器（WinMain 调用 UnityMain2）
GameAssembly.dll → 被 Themida 壳保护（需内存 dump）
global-metadata.dat → IL2CPP 元数据（类名/方法名/字段偏移）
```

### IL2CPP 元数据定位

通过 `global-metadata.dat` 搜索：
- `checker.CheckFlag`
- `checker.VerifyInput`
- `xYz987.check_input`
- `xYz987.AESEncrypt`
- `xYz987.GetRealKey`

### Themida 绕过

GameAssembly.dll 被 Themida 壳保护 → 静态分析不到有效逻辑 → 运行程序后 dump 内存中的 `GameAssembly.dll`（Themida 执行时已脱壳）。

### check_input 逻辑

1. 输入长度必须为 `0x2a`(42)
2. `GetRealKey()` 生成 AES key
3. `AESEncrypt(input, key)` 加密
4. 与 `target_cipher` 比较
5. 相等则成功

### 运行时提取的参数

| 参数 | 值 |
|------|-----|
| target_cipher | `c6788f4ebc79...7b3c68` (64 bytes) |
| key_seed | `asdfg` |
| key_part1 | `9876543210` |
| aes_iv | `1234567890ABCDEF` |

### GetRealKey 生成算法

```python
key_seed = "asdfg"
key_part = "9876543210"[3:8]  # "65432"
key = (key_seed + key_part).ljust(32, '0')  # "asdfg65432" + "\x00"*22
# AES-CBC + PKCS7
```

### 解密脚本

```python
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from cryptography.hazmat.primitives import padding

target = bytes.fromhex("c6788f4ebc791b7572eca04b2a9ccb23f9a1cee4a0dd3ec4e8d64d119fe3ac1bb9cc08893bfba51ee7a3687207b7b3c68")
key = ("asdfg" + "9876543210"[3:8]).ljust(32, "0").encode("ascii")
iv = b"1234567890ABCDEF"

decryptor = Cipher(algorithms.AES(key), modes.CBC(iv)).decryptor()
padded = decryptor.update(target) + decryptor.finalize()
unpadder = padding.PKCS7(128).unpadder()
flag = unpadder.update(padded) + unpadder.finalize()
print(flag.decode())  # flag{cc697aa6-42b6-4341-9bed-6b5cc5c603fd}
```

### 假 Flag 干扰

`global-metadata.dat` 中能搜到另一个假 flag：`flag{f64827be-459a-416c-83bb-b172173f48a9}`
但它加密后与 target_cipher 不匹配。

## Connections
- Related: [[des-apk-rodata]] (APK 逆向 + PE/DEX 分析)
- Related: [[skill-tools]] (dump / 脱壳)
- Source: [[raw/第三届"长城杯"网数智安全大赛(防护赛)决赛-部分WP]]
