---
title: "冰蝎 Webshell 流量解密"
category: misc
tags: [behinder, webshell, traffic-analysis, aes, java, pcap, 冰蝎, 流量解密, AES-ECB, AES-GCM]
triggers: [behinder, 冰蝎, webshell traffic, AES webshell, filter注入, tomcat filter, pcap decrypt]
created: 2026-06-08
updated: 2026-06-08
sources: [raw/2026 软件系统安全赛 初赛 wp.md]
---

# 冰蝎 Webshell 流量解密

## Summary

冰蝎 (Behinder) 是一种常见的 JSP Webshell 管理工具，通过 AES 加密 C2 通信。在流量分析 (PCAP) 场景中，需要从 HTTP 请求中提取加密的 Java Class 字节码，反编译后获取密码和加密逻辑，再解密后续的 AES-GCM C2 通信。

## Key Points

- 冰蝎通过 Tomcat Filter 动态注入实现持久化
- 第一阶段的 Class 字节码通过 AES-ECB 加密，密钥为密码的 MD5 前 16 字节
- 解密后的 Class 反编译可提取密码 (`eac9fa38330a7535`) 和路径 (`/favicondemo.ico`)
- 第二阶段的 C2 通信使用 AES-GCM，密钥从 PCAP 命令行参数中提取
- 植入程序用 PyInstaller 打包 → UPX 压缩 → PyLingual 反编译

## 冰蝎 Filter 结构

```java
public class BehinderFilter extends ClassLoader implements Filter {
    String Pwd = "eac9fa38330a7535";
    String path = "/favicondemo.ico";
    
    // AES-ECB 解密 + defineClass 加载 class
    // addFilter() 动态注册 Tomcat Filter 实现持久化
}
```

`equals()` 方法重写 — 冰蝎通过 `session.putValue("u", this.Pwd)` 触发反序列化时的 `equals()` 调用，在 equals 中完成 Filter 注入。

## AES-ECB Class 解密

```python
from Crypto.Cipher import AES
import hashlib

key = hashlib.md5(PASSWORD.encode()).hexdigest()[:16].encode()
cipher = AES.new(key, AES.MODE_ECB)
plain = unpad(cipher.decrypt(data))
```

## AES-GCM C2 通信解密

```python
from Crypto.Cipher import AES

def decrypt_blob(key, blob):
    nonce = blob[:12]
    ciphertext = blob[12:-16]
    tag = blob[-16:]
    cipher = AES.new(key, AES.MODE_GCM, nonce=nonce)
    return cipher.decrypt_and_verify(ciphertext, tag)
```

## Connections

- Related: [[misc/png-idat-dp-repair]]
- Source: [[ctf/2026-software-security-competition-qualifier]]
