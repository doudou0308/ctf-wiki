---
title: "OtterCTF 内存取证 1-13 全题 WP"
category: forensics
tags: [ctf, forensics, volatility, memory, otterctf, memory-forensics]
triggers: [OtterCTF, memory forensics, volatility, vmem, HiddenTear, lsadump]
created: 2026-06-10
updated: 2026-06-10
sources: [raw/OtterCTF---Memory Forensics内存取证(1-13)-CSDN博客.md]
---

# OtterCTF 内存取证 1-13 全题 WP

## Summary

OtterCTF (otterctf.com) 内存取证专项，共 13 题，覆盖 Volatility 内存分析全流程：密码提取/进程分析/网络连接/注册表/PID 提取/剪贴板/木马分析/文件提取/勒索软件解密。

## 每题考点

| # | 题目 | 核心技法 |
|---|------|---------|
| 1 | What the password? | `lsadump` 提取明文密码（非 hashdump） |
| 2 | General Info | `netscan` 查 IP + 注册表追踪查计算机名 |
| 3 | Play Time | `pslist` 查游戏进程 + `netscan` 查服务器 IP |
| 4 | Name Game | WinHex 搜索游戏频道名 + `strings\|grep` 找用户名 |
| 5 | Name Game 2 | `memdump` 导出进程 → 搜索特定十六进制模式找密码 |
| 6 | Silly Rick | `clipboard` 查看剪贴板获取邮件密码 |
| 7 | Hide And Seek | `pslist` + `cmdline` + `dlllist` 识别恶意软件 |
| 8 | Path To Glory | `filescan\|grep` 定位种子文件 → `dumpfiles` 提取 → 分析来源 |
| 9 | Path To Glory 2 | `memdump` 转储 Chrome → `strings\|grep` 搜索下载链接 |
| 10 | Bit 4 Bit | `procdump` 提取恶意进程 → IDA 分析 C2 地址 |
| 11 | Graphic's For The Weak | `foremost` 从恶意 exe 分离 PNG |
| 12 | Recovery | `strings -eb` 搜索 UTF-16 编码的 computerName-userName 组合 |
| 13 | Closure | HiddenTearDecrypter + 密钥解密 .locked 文件 |

## 关键命令速查

```bash
# 系统信息
vol.py -f OtterCTF.vmem imageinfo

# 密码提取（不是 hashdump）
vol.py -f OtterCTF.vmem --profile Win7SP1x64 lsadump

# 剪贴板
vol.py -f OtterCTF.vmem --profile Win7SP1x64 clipboard

# 进程内存转储
vol.py -f OtterCTF.vmem --profile Win7SP1x64 memdump -p PID -D ./

# 恶意软件提取
vol.py -f OtterCTF.vmem --profile Win7SP1x64 procdump -p PID -D ./

# 注册表追踪
vol.py -f OtterCTF.vmem --profile Win7SP1x64 -o 0x... printkey -K "ControlSet001\..."

# 十六位编码字符串搜索
strings -eb OtterCTF.vmem | grep WIN-LO6FAF3DTFE-Rick
```

## Connections
- Related: [[forensics/skill-memory-forensics]], [[forensics/skill-disk-forensics]]
- Source: [[raw/OtterCTF---Memory Forensics内存取证(1-13)-CSDN博客]]
