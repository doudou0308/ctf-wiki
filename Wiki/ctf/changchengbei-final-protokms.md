---
title: "长城杯决赛渗透 PWN — protokms"
category: ctf
tags: [ctf, 长城杯, pwn, writeup, protobuf, kms, house-of-cat, largebin-attack]
triggers: [protokms, 长城杯决赛, protobuf-c, KMS service]
created: 2026-06-10
updated: 2026-06-10
sources: [raw/长城杯决赛渗透PWN：protokms-先知社区.md]
---

# 长城杯决赛渗透 PWN — protokms (protobuf-c KMS)

> 来源：先知社区 | glibc 2.35 | 不是菜单堆，而是 protobuf-c 封装的 KMS 服务

## 协议还原

`kms.proto` 定义 `CREATE/READ/UPDATE/DELETE` 四种操作，实际格式为 `2-byte LE length + protobuf body`。

请求结构体：

```
message Credential {
  uint32 id = 1;
  string service_name = 2;
  uint32 key_size = 3;
  bytes secret_key = 4;
}
```

## 漏洞点 — CREATE 失败路径留下 dangling pointer

```c
value[id] = calloc(key_size);       // 先申请
value[id] = p;                       // 先写槽位
if (len(secret_key) > key_size)     // 再校验
  free(p);                           // free 但不清零槽位
```

发送 `key_size=0x100, secret_key=超过0x100的字节` → 申请后立刻 free，`value[id]` 保持 dangling pointer。

## READ / UPDATE 利用原语

- **READ**: 仅检查 `value[id] != NULL`，按原长度打包返回 → UAF read
- **UPDATE**: 对 dangling pointer 指向区域 `memcpy` → UAF write
- **DELETE**: 正常释放 + 清空槽位

## 利用链 (glibc 2.35)

```
CREATE失败 → dangling pointer
→ 堆切割做 alias/overlap
→ READ 泄露 libc + heap
→ free fake 0x430 chunk 进 unsorted
→ largebin attack 改 _IO_list_all
→ fake wide FILE + setcontext+0x3d
→ openat2/read/write 拿 flag
```

## Connections
- Related: [[pwn/skill-advanced-exploits]], [[ctf/changchengbei-2026]]
- Source: [[raw/长城杯决赛渗透PWN：protokms-先知社区]]
