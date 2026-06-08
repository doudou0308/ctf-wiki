---
title: "2026 软件系统安全赛 初赛 WP"
category: ctf
tags: [ctf, 软件安全赛, writeup, web, pwn, misc, reverse, crypto]
triggers: [软件系统安全赛, 2026软件安全赛, Spirit Team, Spirit战队]
created: 2026-06-08
updated: 2026-06-08
sources: [raw/2026 软件系统安全赛 初赛 wp.md]
---

# 2026 软件系统安全赛 初赛 Writeup

> 作者：[[Spirit Team]] | 来源：[mp.weixin.qq.com](https://mp.weixin.qq.com/s/EsyWAleuX2wnTh7rLipTHg)
> 日期：2026-03-22 | 题数：7 题 (全赛道 AK)

## 题目一览

| 赛道 | 题目 | 核心技术 | Wiki 页面 |
|------|------|---------|----------|
| Web | Auth | file:// SSRF → Redis CRLF + Pickle RCE + XMLRPC | [[web/file-protocol-redis-crlf-pickle-rce]] |
| Web | themyleaf | PRNG 状态恢复 + Thymeleaf SSTI 绕过 + 7z SUID 提权 | [[web/thymeleaf-ssti-fragment-expression]] [[web/prng-state-recovery]] |
| Pwn | MailSystem | Heap exploitation + IO_FILE stdout FSOP | [[pwn/io-file-stdout-fsop]] |
| Misc | TrafficHunter | 冰蝎 AES 流量解密 + AES-GCM C2 协议还原 | [[misc/behinder-traffic-decryption]] |
| Misc | Steg | PNG IDAT 损坏修复(DP) + LSB + ZIP CRC32 爆破 + 零宽字符 | [[misc/png-idat-dp-repair]] |
| Re | re1 | IDA 静态分析 + SMC 反调试 | - |
| Crypto | rsa | RSA 多级攻击 | - |

## 考点分布

| 考点类别 | 题目 | 技巧 |
|----------|------|------|
| SSRF + 反序列化 | Auth | `file://` 任意文件读、Redis CRLF 协议注入、Pickle 反序列化链 (`getattr` → `os.system`)、XMLRPC RCE |
| SSTI + 提权 | themyleaf | Thymeleaf fragment expression `__\|$${...}\|__::.x` 绕过、PRNG 状态逆向恢复密码、`7z a -ttar -an -so /flag` SUID 读文件 |
| Heap + FSOP | MailSystem | UAF → Fastbin Overlap → IO_FILE stdout 劫持 → FSOP → ORW |
| 流量分析 | TrafficHunter | 冰蝎 Filter 注入分析、AES-ECB 解密 Class 字节码、AES-GCM C2 通信解密 |
| 隐写 | Steg | PNG IDAT 损坏 DP 对齐修复、ZIP CRC32 暴力破解、零宽字符 01 解码 |
| 逆向 | re1 | C++ Loader 分析、SMC 自修改代码 |
| 密码 | rsa | 多级 RSA 攻击链 |

## 解法亮点

1. **Auth pickle 链**：`RestrictedUnpickler` 只放行 `builtins.getattr`，利用 `OnlineUser.__init__.__globals__` 拿到 `os.system`
2. **Redis CRLF 注入**：通过 `avatar_url` 将 HTTP 请求打到 `127.0.0.1:6379`，用 CRLF + RESP 写入恶意 pickle 键
3. **XMLRPC 隐蔽 RCE**：发现 `/opt/mcp_service/` 下的 XMLRPC 服务暴露 `execute_command` 方法，硬编码 token
4. **Thymeleaf 高版本绕过**：`${...}` 被拦截，用 `__|$${...}|__::.x` 字面量拼接绕过
5. **PRNG 逆向**：用户注册返回 PRNG 取值 → 反推内部状态 → 预测 admin 密码
6. **7z SUID 读 flag**：`7z a -ttar -an -so /flag` 将 flag 内容打包为 tar 流输出到 stdout
7. **PNG IDAT DP 修复**：损坏 PNG 的 IDAT 块错位，DP 枚举每行 filter_byte 偏移量选择最优路径重建图像

## Connections
- Source: [[raw/2026 软件系统安全赛 初赛 wp]]
- Related: [[pwn/io-file-stdout-fsop]], [[web/ssrf-gopher-techniques]], [[forensics/multi-layer-steganography]]
