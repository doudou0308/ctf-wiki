---
title: 自定义栈式 VM — Spring Boot JAR 隐藏 VM + Rust AES-CBC + PCAP 解密
category: reverse
tags: [custom-vm, spring-boot, jar, pcap, aes-cbc, rust, jadx, stack-vm, telemetry]
triggers: [自定义VM, 栈式VM, Spring Boot隐藏VM, JADX-MCP, VmContext, AnalyticsReportGenerator, payload.enc, opcode 10-26, Rust ELF AES-CBC, row shifts, PCAP telemetry, tshark 解密, 流量包解密, notjavaweb]
created: 2026-06-07
updated: 2026-06-07
sources: [raw/第三届"长城杯"网数智安全大赛(防护赛)决赛-部分WP.md]
---

## Summary

Spring Boot JAR 中隐藏了一个由用户行为驱动的栈式 VM。HTTP 请求将 VM 指令写入缓冲区 → 登出时触发 VM 执行 → 解密内置 `payload.enc` 得到 Rust ELF → ELF 读取 flag 用自定义 AES-CBC 变体加密 → 通过 `/api/telemetry` 发出 → 从流量包中解密得到 flag。

## Key Points

### VM 架构

| 类 | 角色 |
|----|------|
| `ReviewController` | `/api/reviews/add` — 从评论 `data[...]` 提取参数作为指令 |
| `UserController` | `/api/user/avatar` — `emojiAvatarId` 作为整数指令 |
| `UserBehaviorAnalyticsAspect` | 切面，每条评论数据会进入 VM 两次 |
| `AnalyticsReportGenerator` | `/api/logout` 触发 `generateReport(buffer)` → 执行 VM |
| `VmContext` | 维护栈式 VM 的缓冲区和状态 |

### 关键 Opcode

| Opcode | 操作 | Opcode | 操作 |
|--------|------|--------|------|
| 10 | 读取 resource | 18 | parse int |
| 11 | byte array get | 19 | dup |
| 12 | byte array set | 20 | swap |
| 13 | xor | 21 | jump |
| 14 | 乘法取低 8 bit | 22 | 条件跳转(=0跳转) |
| 15 | 写文件 | 23 | sub |
| 16 | 执行命令 | 24 | add |
| 17 | byte array length | 25 | 栈上偏移取值 |

### payload.enc 解密

```python
key = 102
for i, b in enumerate(data):
    signed_b = b if b < 128 else b - 256
    val = (signed_b ^ key) - i
    data[i] = val & 0xff
    key = val ^ 55
```

解出 Rust ELF64 PIE，SHA256: `5fafafc9718a42cbaa6cb7572c127c843f10b3ee1510d10e2d0dd987ef7f5f26`

### 自定义 AES-CBC 变体

ELF rodata 中提取的 AES 参数：

| 参数 | 值 |
|------|-----|
| IV | `8e1af65530c974bb2d974e1160daa73c` |
| Key | `4a7f2c91b35ed816fa4309cc7be5283d` |
| Row shifts | `[0, 3, 1, 2]` (非标准) |
| S-box | 文件偏移 `0x6970` (256 bytes) |

### 完整解密链

1. `tshark -r traffic.pcap` 提取所有 POST 请求
2. stream 0 → 提取 VM 事件（评论 data + avatarId）
3. stream 1 → 提取 `/api/telemetry` body（AES 密文）
4. 从 JAR 中提取 `payload.enc` → 解密得到 Rust ELF
5. 从 ELF 提取 AES 参数（IV/key/row shifts/S-box）
6. 自定义 AES-CBC 解密 telemetry 密文
7. 正则提取 `flag{...}`

Flag: `flag{F1n@1ly_Y0u_G0t_Th1s_f1ag_and_f1nd_7h3_TRUTH_D0_Y0u_L1k3_1t?}`

## Connections
- Related: [[simple-smc]] (自修改代码)
- Related: [[chacha20-apk-native]] (自定义加密算法逆向)
- Source: [[raw/第三届"长城杯"网数智安全大赛(防护赛)决赛-部分WP]]
