---
title: 日志取证攻击链还原
category: forensics
tags: [log-forensics, apache, mod_dumpio, attack-chain, data-exfiltration]
triggers: [日志取证, Apache access.log, error.log, mod_dumpio, DataMeterAgent, X-Admin-Token, 攻击链, base64url, 数据外传]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/forensics/日志取证攻击链还原.md]
---

## Summary

分析 Apache access.log + mod_dumpio error.log 还原攻击链：提取登录凭证 → 追踪数据导出链路 → 解码外传数据。

## Key Points

- 异常 User-Agent `DataMeterAgent/3.4` 标识攻击工具
- `X-Admin-Token` 标识管理员会话
- `mod_dumpio` 记录完整 HTTP body 是取证金矿

## Details

### 核心代码

```python
import re, base64, urllib.parse

with open('error.log') as f:
    for line in f:
        # 提取登录凭证
        m = re.search(r'username=([^&]+)&password=([^\s]+)', line)
        if m:
            print(f"{unquote(m[1])}:{unquote(m[2])}")
        # 提取 base64 编码数据
        m = re.search(r'&note=([^&\s]+)', line)
        if m:
            b64 = unquote(m[1]).replace('-','+').replace('_','/')
            b64 += '=' * ((4 - len(b64) % 4) % 4)
            data = base64.b64decode(b64)
            if data[:4] == b'PK\x03\x04':
                print("ZIP found!")
```

### 日志取证关键步骤

1. 找异常 UA/IP
2. 提取认证信息
3. 追踪数据流出
4. 还原外传数据

## Connections

- Related: [[covert-channel-icmp-dns]]
- Related: [[data-leak-trace-purge]]
