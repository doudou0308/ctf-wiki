---
title: DES APK 逆向 — .rodata hex 字符串提取
category: reverse
tags: [android, des, jni, rodata, hex-strings, dex, smali, pkcs7, capstone]
triggers: [DES, APK, classes.dex, classes3.dex, .rodata, 666c61677b, PKCS7, hex字符串, IDA搜索, libcrackme2, Android逆向, 反射加载DEX, verifyFlag, des_ecb_decrypt]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/reverse/DES加密验证_APK逆向.md, raw/ctf-solutions/CrackMe_2_3/writeup.md]
---

## Summary

APK 多层 classes.dex 嵌套架构：外层 Activity 反射加载内层 DEX → 内层 DEX 反射调用 Native `verifyFlag()` → `libcrackme2.so` 中 DES-ECB 解密密文 → 与用户输入比对。`.rodata` 段直接存储 hex 编码的 flag 密文，包含 PKCS7 填充。

## Key Points

- APK 逆向时 `.rodata` 段是金矿：搜索 `666c61677b`（`flag{` 的 hex 编码）直接定位加密数据
- 多层 DEX 架构：`classes3.dex` 加载 `assets/classes3.dex` → 反射调用外层 Native 方法
- PKCS7 填充：末尾字节值 = 填充长度，无需执行 DES 解密即可去除
- 此类题目的 shortcut：直接在 `.so` 文件中 hex 解码 `.rodata`，跳过 DES 密钥逆向

## Architecture

```
classes3.dex (外层)
  └─ System.loadLibrary("crackme2")
  └─ PathClassLoader 加载 assets/classes3.dex
       └─ assets/classes3.dex (内层)
            └─ 反射: Class.forName("com.cr.crackme2.MainActivity")
                 .getMethod("verifyFlag").invoke(userInput)
                       └─ libcrackme2.so (Native)
                            ├─ des_ecb_decrypt(ciphertext, 32, DES_KEY, output)
                            ├─ bytesToHex() — 字节转十六进制
                            └─ EncryptedFlag 类 — 存储加密 Flag
```

## Details

### DEX 层分析

**wide.verify@VL (0x86c)** — 验证入口：
```smali
sget v0, wide_mEditFlagId
invoke-virtual {this, v0}, findViewById        → 获取 EditText
invoke-virtual {edit}, getText                 → 获取用户输入
invoke-direct {this, candidate}, wide.callNativeMethod → 调用native验证
move-result v2
if-eqz ok, loc_8AE
  → Snackbar.show("恭喜,这是一个正确的flag")    ← 成功
  return
loc_8AE:
  → Snackbar.show("flag错误")                   ← 失败
```

**wide.callNativeMethod@ZL (0x5b8)** — 反射调用：
```smali
const-string v2, "com.cr.crackme2.MainActivity"
invoke-static {v2}, Class.forName              → 获取外层 MainActivity 类
const-string v3, "verifyFlag"
invoke-virtual {clazz, v3, v4}, Class.getMethod → 获取 verifyFlag 方法
invoke-virtual {method, v5, v4}, Method.invoke  → 调用 native 方法
```

### Native 层分析 (libcrackme2.so)

| 函数 | 地址 | 说明 |
|------|------|------|
| `verifyFlag` | 0x240f0 | JNI 验证函数，接收用户输入 |
| `des_encrypt` | 0x24e10 | DES 单块加密 |
| `des_ecb_decrypt` | 0x25bc0 | DES-ECB 模式解密 |
| `bytesToHex` | (动态导出) | 字节数组转十六进制 |

**Native 库关键字符串：**
```
偏移 0xca8b: "666c61677b323032333332363037373838393039363338307d07070707070707"
偏移 0xcd1d: "Flag %d"
偏移 0x3ca0: "EncryptedFlag" (C++ 类名)
```

### 验证逻辑伪代码

```java
boolean verifyFlag(String input) {
    String storedHex = "666c61677b323032333332363037373838393039363338307d07070707070707";
    byte[] ciphertext = hexToBytes(storedHex);  // 32 bytes
    byte[] plaintext = des_ecb_decrypt(ciphertext, 32, DES_KEY);
    String expected = new String(plaintext).trim();  // 去除 PKCS7 填充
    return input.equals(expected);
}
```

### Flag 提取

```
666c61677b323032333332363037373838393039363338307d07070707070707
  ↓ hex decode
66 6c 61 67 7b 32 30 32 33 33 32 36 30 37 37 38
 f  l  a  g  {  2  0  2  3  3  2  6  0  7  7  8
38 39 30 39 36 33 38 30 7d 07 07 07 07 07 07 07
 8  9  0  9  6  3  8  0  }  PAD PAD PAD PAD PAD PAD PAD

末尾 7 字节 = 0x07 → PKCS7 填充值 = 7
去除填充: flag{2023326077889096380}
```

### 核心脚本

```python
data = open('libcrackme2.so', 'rb').read()
idx = data.find(b'666c61677b323032')
raw = bytes.fromhex(data[idx:idx+64].decode('ascii'))
pad = raw[-1]
flag = raw[:-pad].decode()
print(flag)  # flag{2023326077889096380}
```

## Connections
- Related: [[chacha20-apk-native]]
- Related: [[pyinstaller-cython-reverse]]
- Source: [[raw/ctf-solutions/CrackMe_2_3/writeup]]
