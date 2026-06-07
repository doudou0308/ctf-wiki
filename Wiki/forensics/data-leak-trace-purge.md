---
title: 数据泄露溯源与处置
category: forensics
tags: [forensics, data-leak, csv-analysis, sha256, purge]
triggers: [数据泄露, 溯源, 处置, export_log, transfer_log, external_upload, SHA256, PURGE, NOTIFY, MONITOR]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/forensics/数据泄露溯源处置.md]
---

## Summary

交叉对比导出日志和外发日志定位泄露源头，通过 SHA256 邮箱哈希匹配 + 姓名/脱敏手机号兜底匹配，生成处置清单。

## Key Points

- `transfer_log` 中 `external_upload` 事件 + 非 blocked 目标 = 泄露出口
- SHA256 匹配比姓名匹配优先级高
- 多字段组合匹配需要唯一性校验
- 处置动作：`erase_requested → PURGE`，`risk=high → NOTIFY`，其他 → `MONITOR`

## Details

### 核心逻辑

```python
import hashlib

def email_hash(email):
    return hashlib.sha256(email.strip().lower().encode()).hexdigest()

# 优先：email_hash 匹配
if leak['email']:
    eh = email_hash(leak['email'])
    if eh in hash_index and len(hash_index[eh]) == 1:
        matched = hash_index[eh][0]

# 兜底：姓名 + phone_mask 同时匹配
if matched is None:
    common = [c for c in name_index.get(name, []) if c in phone_index.get(phone, [])]
    if len(common) == 1:
        matched = common[0]
```

## Connections

- Related: [[log-forensics-attack-chain]]
- Related: [[covert-channel-icmp-dns]]
