---
title: "R3CTF 2024 Forensics WP"
category: forensics
tags: [ctf, forensics, r3ctf, memory, wsl, malware, pgp, dotnet-deobfuscation]
triggers: [R3CTF, r3ctf forensics, WSL取证, ext4.vhdx, QuickLZ, RSMEncrypt, de4dot]
created: 2026-06-10
updated: 2026-06-10
sources: [raw/R3CTF2024 Forensics一点点WP.md]
---

# R3CTF 2024 Forensics WP

> 来源：zysgmzb.club | 3 题全一血

## TPA 01 — 14G WSL 磁盘取证

- WSL 磁盘文件位于 `AppData\Local\Packages\Canonical...\LocalState\ext4.vhdx`
- 取证大师打开 WSL 磁盘 → 根目录发现加密文件 `F14G`
- 线索：`SELECT something FROM somewhere with the windows10 lol~` → Windows 本机 MySQL
- Windows MySQL 数据库中找到 key → 解密 F14G 文件

## TPA 02 — 手机钓鱼取证

- PCAP 通信流量中找到钓鱼页面密码 `l0v3_aNd_peace`
- Android 模拟器 SQLite `/data/data/com.android.providers.telephony/databases/` 中找到电话号码
- Flag: `r3ctf{15555215558_l0v3_aNd_peace}`

## TPA 03 — 电脑钓鱼+恶意软件+PGP 解密

- IE 缓存中 `.hta` 文件 → 发现 C2 下载 `hhh.exe` + `duanwufangjia.pdf`
- Gajim 聊天记录发现 PGP 加密 flag 消息
- `hhh.exe` 经 de4dot-cex 去混淆 → 分析出 C2 通信加密逻辑
- C2 加密：QuickLZ 压缩 → RSMEncrypt(AES-CBC, key=123456789 UTF-16, salt=\x00*8)
- 从 PCAP tcp.stream 提取加密流量 → PBKDF2 派生 key/iv → AES-CBC 解密 → QuickLZ 解压
- 解压后获得 gnupg 文件夹 → 读取 PGP 私钥 → 解密 flag

## C2 解密脚本框架

```python
from Crypto.Cipher import AES
from Crypto.Protocol.KDF import PBKDF2

salt = b"\x00" * 8
key_bytes = PBKDF2(b"1\x002\x003\x00...", salt, dkLen=32, count=1)
# key_bytes[:16] 为 AES key, key_bytes[16:] 为 IV
# AES-CBC 解密后 → QuickLZ 解压
```

## Connections
- Related: [[forensics/skill-memory-forensics]], [[reverse/skill-patterns]]
- Source: [[raw/R3CTF2024 Forensics一点点WP]]
