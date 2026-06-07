---
title: ICMP/DNS 双隐写信道
category: forensics
tags: [stego, icmp, dns, network-forensics, aes-ecb]
triggers: [ICMP, TTL隐写, DNS TXT, 隐写, 网络隐写, exfil-cdn, AES-ECB, scapy, covert channel]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/forensics/ICMP_DNS双隐写信道.md]
---

## Summary

ICMP 包 TTL 值 0/1 交替编码二进制数据（AES 密文），DNS TXT 记录返回 Base64 编码的 AES 密钥。

## Key Points

- ICMP ID 区分流量类型：ID=4660 是掩护流量，ID=22136 才是数据信道
- 掩护流量 TTL 固定 64，数据信道 TTL 在 0/1 交替

## Details

### 核心代码

```python
from scapy.all import rdpcap, ICMP, IP, DNS, DNSRR
from Crypto.Cipher import AES
import base64

packets = rdpcap('suspicious_traffic.pcapng')

# Step 1: 从 DNS TXT 提取 AES 密钥
for pkt in packets:
    if pkt.haslayer(DNSRR) and pkt[DNSRR].type == 16:
        aes_key = base64.b64decode(b''.join(pkt[DNSRR].rdata))

# Step 2: TTL 值 0→'0', 1→'1', 组成比特串
ttl_bits = ''.join(str(pkt[IP].ttl) for pkt in packets
    if pkt.haslayer(ICMP) and pkt[IP].src == '10.10.20.33' and pkt[ICMP].id == 22136)
message = bytes(int(ttl_bits[i:i+8], 2) for i in range(0, len(ttl_bits)-7, 8))

# Step 3: AES 解密
ct_hex = message.decode().split('|')[0]
cipher = AES.new(aes_key, AES.MODE_ECB)
flag = unpad(cipher.decrypt(bytes.fromhex(ct_hex)), 16).decode()
```

## Connections

- Related: [[log-forensics-attack-chain]]
- Related: [[data-leak-trace-purge]]
